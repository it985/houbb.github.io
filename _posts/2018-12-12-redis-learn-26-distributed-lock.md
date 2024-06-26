---
layout: post
title: Redis Learn-26-Distributed Lock 分布式锁 Redlock
date: 2018-12-12 11:35:23 +0800
categories: [Redis]
tags: [redis, cache, lock, distributed, distributed-lock, sh]
published: true
---

# 使用Redis实现分布式锁

在许多环境中，分布式锁是一种非常有用的原语，其中不同的进程必须以互斥的方式处理共享资源。

有许多库和博客文章描述如何使用Redis实现DLM（分布式锁管理器），但每个库都使用不同的方法，许多使用比稍微复杂的设计所能实现的更低保证的简单方法。

本页面尝试提供一个更加规范的算法来使用Redis实现分布式锁。

我们提出了一种算法，称为 Redlock，它实现了一个我们认为比单实例方法更安全的DLM。

我们希望社区能够对其进行分析、提供反馈，并将其用作实现更复杂或替代设计的起点。

# 实现

在描述算法之前，这里提供了一些已经可用的实现链接，可以用作参考。

[redisson](https://github.com/redisson/redisson)

[redlock-cpp](https://github.com/jacket-code/redlock-cpp)

# 安全性和活性保证

我们将以仅三个属性来建模我们的设计，从我们的角度来看，这些是有效使用分布式锁所需的最低保证。

安全性属性: 互斥。在任何给定时刻，只有一个客户端可以持有锁。

活性属性 A: 无死锁。最终，总是可以获取锁，即使锁定资源的客户端崩溃或发生分区。

活性属性 B: 容错。只要大多数Redis节点处于运行状态，客户端就能够获取和释放锁。

# 为什么基于故障转移的实现还不够好

为了理解我们想要改进的内容，让我们分析一下大多数基于Redis的分布式锁库的当前状况。

使用Redis锁定资源的最简单方法是在一个实例中创建一个键。通常，该键使用Redis的过期功能创建，以便最终释放（我们列表中的属性2）。当客户端需要释放资源时，它会删除该键。

表面上看这样做效果不错，但是存在问题：这在我们的架构中是一个单点故障。如果Redis主节点宕机会发生什么呢？

嗯，让我们添加一个从节点！并在主节点不可用时使用它。但很遗憾，这并不可行。这样做会导致我们无法实现互斥的安全属性，因为Redis复制是异步的。

- 个人理解

最简单的思维其实就是创建一个 key，用来标识当前资源已经被占用。

但是存在一个问题，如果我们的 master 节点挂掉了，因为数据的同步是异步的，那么就会存在问题。

## 竞态条件

这种模型存在一个明显的竞态条件：

- 客户端A在主节点上获取锁。

- 在键写入传输到从节点之前，主节点崩溃。

- 从节点被提升为主节点。

- 客户端B获取了与客户端A已经持有锁的同一资源的锁。违反了安全性！

有时，在特殊情况下，比如在发生故障期间，多个客户端同时持有锁是完全可以接受的。

如果是这种情况，您可以使用基于复制的解决方案。

否则，我们建议实现本文描述的解决方案。

# 单实例的正确实现

在尝试克服上述单实例设置的限制之前，让我们看看在这种简单情况下如何正确执行，因为在某些应用程序中，偶尔发生竞态条件是可以接受的，而且锁定到单个实例是我们将在此处描述的分布式算法的基础。

要获取锁，可以采用以下方式：

```
SET resource_name my_random_value NX PX 30000
```

该命令只会在键不存在时（NX选项）设置键，并设置键的过期时间为30000毫秒（PX选项）。

键的值设置为“myrandomvalue”。

此值必须在所有客户端和所有锁请求中唯一。

基本上，随机值用于以安全的方式释放锁，使用告诉Redis的脚本：仅当键存在且存储在键中的值正是我期望的值时才删除键。

这通过以下Lua脚本实现：

```c
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

## 确保删除锁的是锁的持有者

这一点很重要，以避免删除由另一个客户端创建的锁。

例如，一个客户端可能获取了锁，在某些操作中被阻塞的时间长于锁的有效期（键将过期的时间），然后删除锁，而该锁已经被另一个客户端获取。

仅使用 `DEL` 是不安全的，因为一个客户端可能会删除另一个客户端的锁。

相反，使用上述脚本，每个锁都被“签名”为一个随机字符串，因此只有当它仍然是由试图删除它的客户端设置的锁时，才会被删除。

注：设置一个随机数，用来标识谁是锁的持有者。

### 如何获取随机字符串？

这个随机字符串应该是什么？

我假设它是来自 `/dev/urandom` 的20字节，但你可以找到更便宜的方法，使它足够唯一以满足你的任务需求。

例如，一个安全的选择是使用 `/dev/urandom` 来种子RC4，并从中生成一个伪随机流。

一个更简单的解决方案是使用Unix时间戳和微秒分辨率，将它与客户端ID连接起来，虽然不太安全，但在大多数环境中可能足以胜任。

注：使用时间戳+客户端唯一标识。如果是多线程，可以再加上线程标识。

### TTL 的设置

我们用作键生存时间的时间被称为“锁的有效期”。 

它既是自动释放时间，也是客户端在另一个客户端可能再次获取锁之前执行所需操作的时间，而不违反技术上的互斥保证，这只限于从获取锁的时刻开始的一段时间窗口。

所以现在我们有了一个良好的方式来获取和释放锁。 

这个系统，在考虑一个由单个、始终可用的实例组成的非分布式系统时，是安全的。

让我们将这个概念扩展到一个分布式系统中，我们在这种情况下没有这样的保证。

# Redlock算法

## 集群环境

在分布式版本的算法中，我们假设有N个Redis主节点。

这些节点是完全独立的，因此我们不使用复制或任何其他隐式协调系统。

我们已经描述了如何在单个实例中安全地获取和释放锁。

我们默认算法将使用此方法在单个实例中获取和释放锁。

在我们的示例中，我们将N设为5，这是一个合理的值，因此我们需要在不同的计算机或虚拟机上运行5个Redis主节点，以确保它们基本上以独立的方式失败。

## 获取锁的步骤

为了获取锁，客户端执行以下操作：

1. 它获取当前时间的毫秒数。

2. 它依次在所有N个实例中尝试获取锁，使用相同的键名和随机值在所有实例中。在步骤2中，当在每个实例中设置锁时，客户端使用一个相对于总锁自动释放时间很小的超时来获取它。例如，如果自动释放时间为10秒，则超时可以在 ~ 5-50毫秒范围内。这可以防止客户端在尝试与宕机的Redis节点通信时长时间被阻塞：如果一个实例不可用，我们应该尽快尝试与下一个实例通信。

3. 客户端通过从步骤1中获取的时间戳中减去当前时间来计算获取锁所消耗的时间。只有在客户端能够在大多数实例（至少3个）中获取锁，并且获取锁所消耗的总时间少于锁的有效时间时，才认为锁已获取。

4. 如果成功获取了锁，则其有效时间被视为初始有效时间减去步骤3中计算的已消耗时间。

5. 如果客户端由于某种原因未能获取锁（无法锁定 `N/2+1` 个实例或有效时间为负），则它将尝试解锁所有实例（甚至是它认为自己无法锁定的实例）。


## 算法是否是异步的？

该算法依赖于以下假设：虽然进程之间没有同步时钟，但是每个进程中的本地时间以大致相同的速率流动，误差与锁的自动释放时间相比很小。

这个假设非常类似于现实世界的计算机：每台计算机都有一个本地时钟，我们通常可以依赖于不同计算机的时钟漂移很小。

在这一点上，我们需要更好地指定我们的互斥规则：只要持有锁的客户端在锁的有效时间内完成其工作（如步骤3中获取的），减去一些时间（只是几毫秒，以补偿进程之间的时钟漂移），就可以保证互斥性。

有关需要绑定时钟漂移的类似系统的更多信息，请参阅此论文：[Leases: an efficient fault-tolerant mechanism for distributed file cache consistency.](https://dl.acm.org/citation.cfm?id=74870)

## 失败时重试

当客户端无法获取锁时，应该在随机延迟后重试，以尝试使尝试同时获取同一资源的多个客户端的锁的行为不同步（这可能导致脑裂条件，其中没有人获胜）。

此外，客户端尝试在大多数Redis实例中获取锁的速度越快，脑裂条件（和重试的需求）的窗口就越小，因此理想情况下，客户端应尝试使用多路复用同时向N个实例发送SET命令。

值得强调的是，对于未能获取大多数锁的客户端来说，尽快释放（部分）获取的锁非常重要，以便不需要等待键过期以重新获取锁（但是如果发生网络分区并且客户端无法与Redis实例通信，则需要等待键过期会有可用性惩罚）。

- 个人理解

如果网络不通，可以通过服务器设置的 ttl 来清除锁。

尽可能保证单词的操作消耗的时间短。

## Releasing the lock

Releasing the lock is simple and involves just releasing the lock in all instances, whether or not the client believes it was able to successfully lock a given instance.

ps: 就是客户端很自信，一定会成功。

## 安全性论证

这个算法是安全的吗？

我们可以尝试理解不同情况下会发生什么。

首先，让我们假设一个客户端能够在大多数实例中获取锁。

所有实例将包含一个具有相同生存时间的键。

然而，这些键是在不同的时间设置的，因此这些键也会在不同的时间过期。

但是，如果第一个键最差在时间T1（我们在联系第一个服务器之前采样的时间）设置，而最后一个键最差在时间T2（我们从最后一个服务器获得回复的时间）设置，我们可以确保在集合中最先过期的第一个键将至少存在 `MIN_VALIDITY=TTL-(T2-T1)-CLOCK_DRIFT` 的时间。所有其他键将在稍后过期，因此我们可以确保这些键将同时设置至少这么长时间。

在大多数键被设置的时间段内，另一个客户端将无法获取锁，因为如果已经存在N/2+1个键，则无法成功执行N/2+1个 `SET NX` 操作。

因此，如果已经获取了锁，则不可能在同一时间重新获取它（违反了互斥性质）。

然而，我们还想确保同时尝试获取锁的多个客户端不能同时成功。

如果一个客户端使用的时间接近或大于锁的最大有效时间（我们用于SET的TTL基本上），它将认为锁无效，并将解锁实例，因此我们只需要考虑客户端能够在少于有效时间的时间内锁定大多数实例的情况。

在这种情况下，根据上面已经表达的论点，对于 `MIN_VALIDITY`，没有客户端应该能够重新获取锁。

因此，只有当锁定大多数实例的时间大于TTL时间时，多个客户端才能同时锁定N/2+1个实例（其中“时间”是步骤2结束的时间），从而使锁无效。

您能否提供安全性的形式证明，指出类似的现有算法，或发现错误？这将非常感激。

## 活性性论证

系统的活性性基于三个主要特征：

1. 锁的自动释放（因为键过期）：最终键再次可用以被锁定。

2. 客户端通常会合作，当锁未被获取时或锁被获取并且工作已完成时，删除锁，这样我们很可能不必等待键过期以重新获取锁。

3. 当客户端需要重试锁时，它会等待一个比获取大多数锁所需时间更长的时间，以概率上使资源争用期间的脑裂条件不太可能发生。

然而，我们在网络分区上付出了等于TTL时间的可用性惩罚，因此如果存在持续分区，我们可能会无限期地支付此惩罚。

每当客户端获取锁并在能够删除锁之前被分区时，都会发生这种情况。

基本上，如果存在无限持续的网络分区，系统可能会在无限长的时间内变得不可用。

# 性能、崩溃恢复和fsync

## 如何提升性能

许多将Redis用作锁服务器的用户需要高性能，即获取和释放锁的延迟以及每秒执行的获取/释放操作数量都需要较高。为了满足这一要求，与N个Redis服务器通信以降低延迟的策略绝对是多路复用（或穷人版的多路复用，即将套接字设置为非阻塞模式，发送所有命令，然后稍后读取所有命令，假设客户端和每个实例之间的往返时间大致相同）。

## 崩溃恢复

然而，如果我们想要实现一个崩溃恢复系统模型，那么还有另一个关于持久性的考虑。基本上，要了解这里的问题，我们假设完全不配置Redis持久性。一个客户端在5个实例中获取了锁中的3个实例。其中一个客户端能够获取锁的实例重新启动，此时我们可以再次为同一资源锁定3个实例，并且另一个客户端可以再次锁定它，违反了锁的排他性属性。

### AOF 持久化

如果我们启用AOF持久化，情况会有所改善。例如，我们可以通过发送`SHUTDOWN`并重新启动服务器来升级服务器。由于Redis过期是语义上实现的，因此即使服务器关闭时也会虚拟地流逝时间，我们的所有要求都是良好的。然而，只要是干净的关闭，一切都很好。

### 如果发生停电怎么办？

如果Redis配置为默认情况下每秒在磁盘上进行一次fsync，那么在重新启动后我们的键可能会丢失。理论上，如果我们想要在任何类型的实例重新启动时保证锁的安全性，我们需要在持久性设置中启用`fsync=always`。这反过来会完全毁掉性能，使其降至传统上用于以安全方式实现分布式锁的CP系统的水平。

注：这种刷新的代价就是性能急剧下降。

### 现实往往更加乐观

然而，事情并不像乍看起来的那样糟糕。

基本上，只要在实例崩溃后重新启动时，它不再参与任何当前活动的锁，那么算法的安全性就能得到保持，因此，当实例重新加入系统时，当前活动的所有锁都是通过锁定其他实例而获得的。

为了保证这一点，我们只需要在崩溃后的一段时间内使实例不可用，时间略长于我们使用的最大TTL。这段时间是指，使得在实例崩溃时存在的所有锁的键变为无效并自动释放所需的时间。

利用延迟重启，基本上可以在没有任何Redis持久性可用的情况下实现安全性，但是请注意，这可能会导致可用性惩罚。

例如，如果大多数实例崩溃，系统将在TTL期间全局不可用（这里的全局意味着在此期间根本无法锁定任何资源）。

ps: 延时重启可以保证算法的安全性。

# 提高算法可靠性：延长锁定时间

如果客户端执行的工作由多个小步骤组成，那么可以默认使用较短的锁定有效时间，并扩展算法以实现锁定扩展机制。

基本上，如果客户端在计算过程中锁的有效时间即将到期时，可以通过向所有实例发送Lua脚本来延长锁的有效时间。

如果键存在且其值仍然是客户端在获取锁时分配的随机值，则将键的TTL延长。

只有在客户端能够在大多数实例中延长锁的情况下，且在有效时间内，客户端才应该考虑重新获取锁（基本上使用的算法与获取锁时使用的算法非常相似）。

然而，这在技术上并不改变算法，因此应该限制重新获取锁的最大次数，否则将违反活性性质之一。

# 拓展阅读

[How to do distributed locking](http://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html)

[Is Redlock safe?](http://antirez.com/news/101)

# 拓展阅读

[ZooKeeper 分布式锁](https://houbb.github.io/2018/09/08/distributed-lock-zookeeper)

[SQL 分布式锁](https://houbb.github.io/2018/09/08/distributed-lock-sql)

[Redis 分布式锁设计](https://houbb.github.io/2019/01/07/redis-lock)

# 参考资料

[distlock](https://redis.io/topics/distlock)

[Redis 分布式锁进化史解读+缺陷分析](https://mp.weixin.qq.com/s/8FYMUpaBcgOZ9lEqt-0PKg)

* any list
{:toc}