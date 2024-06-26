---
layout: post
title: 分布式一致性原理与实践-03-Paxos 协议实战之 Chubby
date:  2023-03-07 +0800
categories: [Distributed]
tags: [distributed, learn, paxos, sh]
published: true
---

# Paxos 的工程实践

在第2章中，我们主要从理论上讲解了Paxos算法，然而Paxos算法在工程实现的过程中，会遇到非常多的问题。

Paxos算法描述并没有涉及实际工程中需要注意的很多细节，同时对于开发人员来说，如何在保证数据一致性的情况下兼顾稳定性和性能也是一个巨大的挑战。

从本章开始，我们将结合实际工程实践中的Paxos实现，来讲解如何真正地使用Paxos算法来解决分布式一致性问题。

# Chubby

Google Chubby 是一个大名鼎鼎的分布式锁服务，GFS和Big Table等大型系统都用它来解决分布式协作、元数据存储和 Master 选举等一系列与分布式锁服务相关的问题。

Chubby的底层一致性实现就是以Paxos算法为基础的，这给Paxos算法的学习者提供了一个理论联系的范例，从而可以了解到Paxos算法是如何在实际工程中得到应用的。

在本节中，我们将围绕 Google 公开的 Chubby 论文 The Chubby lock service for loosely-coupled distributed systems[1]来讲解Paxos算法在Chubby中的应用。

## 概述

Chubby 是一个面向松耦合分布式系统的锁服务，通常用于为一个由大量小型计算机构成的松耦合分布式系统提供高可用的分布式锁服务。

一个分布式锁服务的目的是允许它的客户端进程同步彼此的操作，并对当前所处环境的基本状态信息达成一致。

针对这个目的，Chubby 提供了粗粒度的分布式锁服务，开发人员不需要使用复杂的同步协议，而是直接调用 Chubby 的锁服务接口即可实现分布式系统中多个进程之间粗粒度的同步控制，从而保证分布式数据的一致性。

Chubby的客户端接口设计非常类似于UNIX文件系统结构，应用程序通过Chubby的客户端接口，不仅能够对 Chubby 服务器上的整个文件进行读写操作，还能够添加对文件节点的锁控制，并且能够订阅Chubby服务端发出的一系列文件变动的事件通知。

## 应用场景

在 Chubby 的众多应用场景中，最为典型的就是集群中服务器的 Master 选举。

例如在Google文件系统（Google File System，GFS）[2]中使用Chubby锁服务来实现对GFS Master服务器的选举。

而在BigTable[3] 中，Chubby同样被用于Master选举，并且借助Chubby，Master 能够非常方便地感知到其所控制的那些服务器。

同时，通过 Chubby，BigTable的客户端还能够方便地定位到当前 Bigtable 集群的 Master 服务器。

此外，在 GFS 和Bigtable中，都使用Chubby来进行系统运行时元数据的存储。

## 设计目标

对于Chubby的设计，有的开发人员觉得作为Paxos算法的实现者，Chubby应该构建成一个包含 Paxos 算法的协议库，从而使应用程序能够便捷地使用 Pasox 算法。

但是，Chubby的最初设计者并没有选择这样做，而是将Chubby设计成一个需要访问中心化节点的分布式锁服务。

Chubby 之所以设计成这样一个完整的分布式锁服务，是因为锁服务具有以下 4 个传统算法库所不具有的优点。

- 对上层应用程序的侵入性更小

对于应用程序，尤其是上层的业务系统来说，在系统开发初期，开发人员并没有为系统的高可用性做好充分的考虑。

事实上，绝大部分的系统一开始都是从一个只需要支撑较小的负载，并且只需要保证大体可用的原型开始的，往往并没有在代码层面为分布式一致性协议的实现留有余地。

当系统提供的服务日趋成熟，并且得到一定规模的用户认可之后，系统的可用性就会变得越来越重要了。于是，集群中副本复制和 Master 选举等一系列提高分布式系统可用性的措施，就会被加入到一个已有的系统中去。

在这种情况下，尽管这些措施都可以通过一个封装了分布式一致性协议的客户端库来完成，但相比之下，使用一个分布式锁服务的接口方式对上层应用程序的侵入性会更小，并且更易于保持系统已有的程序结构和网络通信模式。

- 便于提供数据的发布与订阅

几乎在所有使用Chubby进行Master选举的应用场景中，都需要一种广播结果的机制，用来向所有的客户端公布当前的Master服务器。

这就意味着Chubby应该允许其客户端在服务器上进行少量数据的存储与读取——也就是对小文件的读写操作。

虽然这个特性也能够通过一个分布式命名服务来实现，但是根据实际的经验来看，分布式锁服务本身也非常适合提供这个功能，这一方面能够大大减少客户端依赖的外部服务，另一方面，数据的发布与订阅功能和锁服务在分布式一致性特性上是相通的。

- 开发人员对基于锁的接口更为熟悉

对于绝大部分的开发人员来说，在平常的编程过程中，他们对基于锁的接口都已经非常熟悉了。

因此，Chubby 为其提供了一套近乎和单机锁机制一致的分布式锁服务接口，这远比提供一个一致性协议的库来得更为友好。

- 更便捷地构建更可靠的服务

通常一个分布式一致性算法都需要使用 Quorum 机制来进行数据项值的选定。

Quorum 机制是分布式系统中实现数据一致性的一个比较特殊的策略，它指的是在一个由若干个机器组成的集群中，在一个数据项值的选定过程中，要求集群中存在过半的机器达成一致，因此Quorum机制也被称作“过半机制”。在Chubby中通常使用5台服务器来组成一个集群单元（cell），根据Quorum机制，只要整个集群中有 3 台服务器是正常运行的，那么整个集群就可以对外提供正常的服务。

相反的，如果仅提供一个分布式一致性协议的客户端库，那么这些高可用性的系统部署都将交给开发人员自己来处理，这无疑提高了成本。

因此，Chubby被设计成一个需要访问中心化节点的分布式锁服务。

同时，在Chubby的设计过程中，提出了以下几个设计目标。

- 供一个完整的、独立的分布式锁服务，而非仅仅是一个一致性协议的客户端库

在上面的内容中我们已经讲到，提供一个独立的锁服务的最大好处在于，Chubby对于使用它的应用程序的侵入性非常低，应用程序不需要修改已有程序的结构即可使用分布式一致性特性。

例如，对于“Master选举同时将Master信息登记并广播”的场景，应用程序只需要向Chubby请求一个锁，并且在获得锁之后向相应的锁文件写入 Master 信息即可，其余的客户端就可以通过读取这个锁文件来获取 Master信息。

- 提供粗粒度的锁服务

Chubby 锁服务针对的应用场景是客户端获得锁之后会进行长时间持有（数小时或数天），而非用于短暂获取锁的场景。针对这种应用场景，当锁服务短暂失效时（例如服务器宕机），Chubby需要保持所有锁的持有状态，以避免持有锁的客户端出现问题。这和细粒度锁的设计方式有很大的区别，细粒度锁通常设计为锁服务一旦失效就释放所有锁，因为细粒度锁的持有时间很短，相比而言放弃锁带来的代价较小。

- 在提供锁服务的同时提供对小文件的读写功能

Chubby提供对小文件的读写服务，以使得被选举出来的Master可以在不依赖额外服务的情况下，非常方便地向所有客户端发布自己的状态信息。

具体的，当一个客户端成功获取到一个Chubby文件锁而成为Master之后，就可以继续向这个文件里写入Master信息，其他客户端就可以通过读取这个文件得知当前的Master信息。

- 高可用、高可靠

在Chubby的架构设计中，允许运维人员通过部署多台机器（一般是5台机器）来组成一个Chubby集群，从而保证集群的高可用。基于对Paxos算法的实现，对于一个由5台机器组成的Chubby集群来说，只要保证存在3台正常运行的机器，整个集群对外服务就能保持可用。

另外，由于Chubby支持通过小文件读写服务的方式来进行Master选举结果的发布与订阅，因此在 Chubby 的实际应用过程中，必须能够支撑成百上千个 Chubby 客户端对同一个文件进行监视和读取。

- 提供事件通知机制

在实际使用过程中，Chubby客户端需要实时地感知到Master的变化情况，当然这可以通过让客户端反复的轮询来实现，但是在客户端规模不断增大的情况下，客户端主动轮询的实时性效果并不理想，且对服务器性能和网络带宽压力都非常大。

因此，Chubby 需要有能力将服务端的数据变化情况（例如文件内容变更）以事件的形式通知到所有订阅的客户端。

# Chubby技术架构

接下来我们一起看看，Chubby是如何来实现一个高可用的分布式锁服务的。

## 系统结构

Chubby的整个系统结构主要由服务端和客户端两部分组成，客户端通过RPC调用与服务端进行通信。

一个典型的Chubby集群，或称为Chubby cell，通常由5台服务器组成。这些副本服务器采用Paxos协议，通过投票的方式来选举产生一个获得过半投票的服务器作为Master。

一旦某台服务器成为了 Master，Chubby 就会保证在一段时期内不会再有其他服务器成为Master——这段时期被称为Master租期（Master lease）。

在运行过程中，Master服务器会通过不断续租的方式来延长 Master 租期，而如果 Master 服务器出现故障，那么余下的服务器就会进行新一轮的 Master 选举，最终产生新的 Master 服务器，开始新的Master租期。

集群中的每个服务器都维护着一份服务端数据库的副本，但在实际运行过程中，只有Master 服务器才能对数据库进行写操作，而其他服务器都是使用 Paxos 协议从 Master服务器上同步数据库数据的更新。

现在，我们再来看下 Chubby 的客户端是如何定位到 Master 服务器的。

Chubby 客户端通过向记录有Chubby服务端机器列表的DNS来请求获取所有的Chubby服务器列表，然后逐个发起请求询问该服务器是否是 Master。

在这个询问过程中，那些非 Master 的服务器，则会将当前Master所在的服务器标识反馈给客户端，这样客户端就能够非常快速地定位到Master服务器了。

一旦客户端定位到Master服务器之后，只要该 Master正常运行，那么客户端就会将所有的请求都发送到该Master服务器上。针对写请求，Chubby Master会采用一致性协议将其广播给集群中所有的副本服务器，并且在过半的服务器接受了该写请求之后，再响应给客户端正确的应答。而对于读请求，则不需要在集群内部进行广播处理，直接由Master服务器单独处理即可。

在Chubby运行过程中，服务器难免会发生故障。如果当前的Master服务器崩溃了，那么集群中的其他服务器会在Master租期到期后，重新开启新一轮的Master选举。

通常，进行一次 Master 选举大概需要花费几秒钟的时间。而如果是集群中任意一台非 Master服务器崩溃，那么整个集群是不会停止工作的，这个崩溃的服务器会在恢复之后自动加入到 Chubby 集群中去。

新加入的服务器首先需要同步 Chubby 最新的数据库数据，完成数据同步之后，新的服务器就可以加入到正常的Paxos运作流程中与其他服务器副本一起协同工作。

如果集群中的一个服务器发生崩溃并在几小时后仍无法恢复正常，那么就需要加入新的机器，并同时更新DNS列表。

Chubby 服务器的更换方式非常简单，只需要启动Chubby服务端程序，然后更新 DNS 上的机器列表（即使用新机器的 IP 地址替换老机器的 IP地址）即可。

在Chubby运行过程中，Master服务器会周期性地轮询DNS列表，因此其很快就会感知到服务器地址列表的变更，然后Master就会将集群数据库中的地址列表做相应的变更，集群内部的其他副本服务器通过复制方式就可以获取到最新的服务器地址列表了。

## 目录与文件

Chubby 对外提供了一套与 Unix 文件系统非常相近但是更简单的访问接口。

Chubby 的数据结构可以看作是一个由文件和目录组成的树，其中每一个节点都可以表示为一个使用斜杠分割的字符串，典型的节点路径表示如下：

```
/ls/foo/wombat/pouch
```

其中，ls是所有Chubby节点所共有的前缀，代表着锁服务，是Lock Service的缩写；foo则指定了 Chubby 集群的名字，从 DNS 可以查询到由一个或多个服务器组成该 Chubby集群；剩余部分的路径/wombat/pouch则是一个真正包含业务含义的节点名字，由Chubby服务器内部解析并定位到数据节点。

Chubby的命名空间，包括文件和目录，我们称之为节点（nodes，在本书后面的内容中，我们以数据节点来泛指Chubby的文件或目录）。

在同一个Chubby集群数据库中，每一个节点都是全局唯一的。和 Unix 系统一样，每个目录都可以包含一系列的子文件和子目录列表，而每个文件中则会包含文件内容。当然，Chubby 并非模拟一个完整的文件系统，因此没有符号链接和硬连接的概念。

由于 Chubby 的命名结构组成了一个近似标准文件系统的视图，因此 Chubby 的客户端应用程序也可以通过自定义的文件系统访问接口来访问 Chubby 服务端数据，比如可以使用GFS的文件系统访问接口，这就大大减少了用户使用Chubby的成本。

Chubby 上的每个数据节点都分为持久节点和临时节点两大类，其中持久节点需要显式地调用接口API来进行删除，而临时节点则会在其对应的客户端会话失效后被自动删除。

也就是说，临时节点的生命周期和客户端会话绑定，如果该临时节点对应的文件没有被任何客户端打开的话，那么它就会被删除掉。因此，临时节点通常可以用来进行客户端会话有效性的判断依据。

另外，Chubby 上的每个数据节点都包含了少量的元数据信息，其中包括用于权限控制的访问控制列表（ACL）信息。同时，每个节点的

元数据中还包括 4 个单调递增的 64位编号，分别如下。

- 实例编号

实例编号用于标识 Chubby 创建该数据节点的顺序，节点的创建顺序不同，其实例编号也不同，因此，通过实例编号，即使针对两个名字相同的数据节点，客户端也能够非常方便地识别出是否是同一个数据节点——因为创建时间晚的数据节点，其实例编号必定大于任意先前创建的同名节点。

- 文件内容编号（只针对文件）：文件内容编号用于标识文件内容的变化情况，该编号会在文件内容被写入时增加。

- 锁编号

锁编号用于标识节点锁状态变更情况，该编号会在节点锁从自由（free）状态转换到被持有（held）状态时增加。

- ACL编号

ACL编号用于标识节点的ACL信息变更情况，该编号会在节点的ACL配置信息被写入时增加。

同时，Chubby还会标识一个64位的文件内容校验码，以便客户端能够识别出文件是否变更。

## 锁与锁序列器

在分布式系统中，锁是一个非常复杂的问题，由于网络通信的不确定性，导致在分布式系统中锁机制变得非常复杂，消息的延迟或是乱序都有可能会引起锁的失效。

一个典型的分布式锁错乱案例是，一个客户端 C1获取到了互斥锁 L，并且在锁 L 的保护下发出请求 R，但请求 R 迟迟没有到达服务端（可能出现网络延时或反复重发等），这时应用程序会认为该客户端进程已经失败，于是便会为另一个客户端C2分配锁L，然后再重新发起之前的请求 R，并成功地应用到了服务器上。

此时，不幸的事情发生了，客户端C1发起的请求 R 在经过一波三折之后也到达了服务端，此时，它有可能会在不受任何锁控制的情况下被服务端处理，从而覆盖了客户端 C2的操作，于是导致系统数据出现不一致。

当然，诸如此类消息接收顺序紊乱引起的数据不一致问题已经在人们对分布式计算的长期研究过程中得到了很好的解决，典型的解决方案包括虚拟时间和虚拟同步。

这两个分布式系统中典型的解决方案并不是本书的重点，感兴趣的读者可以在互联网上了解更多相关的参考资料[4]。

在 Chubby 中，任意一个数据节点都可以充当一个读写锁来使用：一种是单个客户端以排他（写）模式持有这个锁，另一种则是任意数目的客户端以共享（读）模式持有这个锁。

同时，在Chubby的锁机制中需要注意的一点是，Chubby舍弃了严格的强制锁，客户端可以在没有获取任何锁的情况下访问Chubby的文件，也就是说，持有锁F既不是访问文件F的必要条件，也不会阻止其他客户端访问文件F。

在 Chubby 中，主要采用锁延迟和锁序列器两种策略来解决上面我们提到的由于消息延迟和重排序引起的分布式锁问题。

其中锁延迟是一种比较简单的策略，使用 Chubby 的应用几乎不需要进行任何的代码修改。

具体的，如果一个客户端以正常的方式主动释放了一个锁，那么 Chubby 服务端将会允许其他客户端能够立即获取到该锁。

而如果一个锁是因为客户端的异常情况（如客户端无响应）而被释放的话，那么 Chubby 服务器会为该锁保留一定的时间，我们称之为“锁延迟”（lock-delay），在这段时间内，其他客户端无法获取这个锁。

锁延迟措施能够很好地防止一些客户端由于网络闪断等原因而与服务器暂时断开的场景出现。

总的来说，该方案尽管不完美，但是锁延时能够有效地保护在出现消息延时情况下发生的数据不一致现象。

Chubby提供的另一种方式是使用锁序列器，当然该策略需要Chubby的上层应用配合在代码中加入相应的修改逻辑。

任何时候，锁的持有者都可以向 Chubby 请求一个锁序列器，其包括锁的名字、锁模式（排他或共享模式），以及锁序号。

当客户端应用程序在进行一些需要锁机制保护的操作时，可以将该锁序列器一并发送给服务端。

Chubby 服务端接收到这样的请求后，会首先检测该序列器是否有效，以及检查客户端是否处于恰当的锁模式；如果没有通过检查，那么服务端就会拒绝该客户端请求。

## Chubby 中的事件通知机制

为了避免大量客户端轮询Chubby服务端状态所带来的压力，Chubby提供了事件通知机制。

Chubby 的客户端可以向服务端注册事件通知，当触发这些事件的时候，服务端就会向客户端发送对应的事件通知。

在 Chubby 的事件通知机制中，消息通知都是通过异步的方式发送给客户端的，常见的Chubby事件如下。

- 文件内容变更

例如，BigTable 集群使用 Chubby 锁来确定集群中的哪台 BigTable 机器是 Master；获得锁的BigTable Master会将自身信息写入Chubby上对应的文件中。

BigTable集群中的其他客户端可以通过监视这个 Chubby 文件的变化来确定新的 BigTable Master机器。

- 节点删除

当 Chubby 上指定节点被删除的时候，会产生“节点删除”事件，这通常在临时节点中比较常见，可以利用该特性来间接判断该临时节点对应的客户端会话是否有效。

- 子节点新增、删除

当Chubby上指定节点的子节点新增或是减少时，会产生“子节点新增、删除”事件。

- Master服务器转移

当Chubby服务器发生Master转移时，会以事件的形式通知客户端。

## Chubby中的缓存

为了提高 Chubby 的性能，同时也是为了减少客户端和服务端之间频繁的读请求对服务端的压力，Chubby 除了提供事件通知机制之外，还在客户端中实现了缓存，会在客户端对文件内容和元数据信息进行缓存。

使用缓存机制在提高系统整体性能的同时，也为系统带来了一定的复杂性，其中最主要的问题就是应该如何保证缓存的一致性。

在Chubby中，通过租期机制来保证缓存的一致性。

Chubby缓存的生命周期和Master租期机制紧密相关，Master会维护每个客户端的数据缓存情况，并通过向客户端发送过期信息的方式来保证客户端数据的一致性。

在这种机制下，Chubby就能够保证客户端要么能够从缓存中访问到一致的数据，要么访问出错，而一定不会访问到不一致的数据。

具体的，**每个客户端的缓存都有一个租期，一旦该租期到期，客户端就需要向服务端续订租期以继续维持缓存的有效性**。

当文件数据或元数据信息被修改时，Chubby服务端首先会阻塞该修改操作，然后由Master向所有可能缓存了该数据的客户端发送缓存过期信号，以使其缓存失效，等到Master在接收到所有相关客户端针对该过期信号的应答（应答包括两类，一类是客户端明确要求更新缓存，另一类则是客户端允许缓存租期过期）后，再继续进行之前的修改操作。

通过上面这个缓存机制的介绍，相信读者都已经明白了，Chubby 的缓存数据保证了强一致性。

尽管要保证严格的数据一致性对于性能的开销和系统的吞吐影响很大，但由于弱一致性模型在实际使用过程中极容易出现问题，因此 Chubby 在设计之初就决定了选择强一致性模型。

### 会话和会话激活（KeepAlive）

Chubby客户端和服务端之间通过创建一个TCP连接来进行所有的网络通信操作，我们将这一连接称为会话（Session）。

会话是有生命周期的，存在一个超时时间，在超时时间内，Chubby 客户端和服务端之间可以通过心跳检测来保持会话的活性，以使会话周期得到延续，我们将这个过程称为 KeepAlive （会话激活）。

如果能够成功地通过KeepAlive过程将Chubby会话一直延续下去，那么客户端创建的句柄、锁和缓存数据等都依然有效。

### KeepAlive请求

下面我们就重点来看看 Chubby Master 是如何处理客户端的 KeepAlive 请求的。

Master在接收到客户端的KeepAlive请求时，首先会将该请求阻塞住，并等到该客户端的当前会话租期即将过期时，才为其续租该客户端的会话租期，之后再向客户端响应这个KeepAlive 请求，并同时将最新的会话租期超时时间反馈给客户端。

Master 对于会话续租时间的设置，默认是12秒，但这不是一个固定的值，Chubby会根据实际的运行情况，自行调节该周期的长短。

举个例子来说，如果当前Master处于高负载运行状态的话，那么Master会适当地延长会话租期的长度，以减少客户端KeepAlive请求的发送频率。

客户端在接收到来自 Master 的续租响应后，会立即发起一个新的 KeepAlive 请求，再由Master进行阻塞。因此我们可以看出，在正常运行过程中，每一个Chubby客户端总是会有一个KeepAlive请求阻塞在Master服务器上。

除了为客户端进行会话续租外，Master 还将通过 KeepAlive 响应来传递 Chubby 事件通知和缓存过期通知给客户端。

具体的，如果Master发现服务端已经触发了针对该客户端的事件通知或缓存过期通知，那么会提前将KeepAlive响应反馈给客户端。

### 会话超时

谈到会话租期，Chubby的客户端也会维持一个和Master端近似相同的会话租期。

为什么是近似相同呢？

这是因为客户端必须考虑两方面的因素：一方面，KeepAlive 响应在网络传输过程中会花费一定的时间；另一方面，Master服务端和Chubby客户端存在时钟不一致性现象。

因此在Chubby会话中，存在Master端会话租期和客户端本地会话租期。

如果 Chubby 客户端在运行过程中，按照本地的会话租期超时时间，检测到其会话租期已经过期却尚未接收到Master的KeepAlive响应，那么这个时候，它将无法确定Master服务端是否已经中止了当前会话，我们称这个时候客户端处于“危险状态”。此时，Chubby客户端会清空其本地缓存，并将其标记为不可用。

同时，客户端还会等待一个被称作“宽限期”的时间周期，这个宽限期默认是 45 秒。如果在宽限期到期前，客户端和服务端之间成功地进行了 KeepAlive，那么客户端就会再次开启本地缓存，否则，客户端就会认为当前会话已经过期了，从而中止本次会话。

我们再着重来看看上面提到的“危险状态”。当客户端进入上述提到的危险状态时，Chubby 的客户端库会通过一个“jeopardy”事件来通知上层应用程序。

如果恢复正常，客户端同样会以一个“safe”事件来通知应用程序可以继续正常运行了。

但如果客户端最终没能从危险状态中恢复过来，那么客户端会以一个“expired”事件来通知应用程序当前Chubby会话已经超时。

Chubby通过这些不同的事件类型通知，能够很好地辅助上层应用程序在不明确 Chubby 会话状态的情况下，根据不同的事件类型来做出不同的处理：等待或重启。

有了这样的机制保证之后，对于那些在短时间内 Chubby 服务不可用的场景下，客户端应用程序可以选择等待，而不是重启，这对于那些重启整个应用程序需要花费较大代价的系统来说非常有帮助。

### Chubby Master 故障恢复

Chubby的Master服务器上会运行着会话租期计时器，用来管理所有会话的生命周期。

如果在运行过程中 Master 出现了故障，那么该计时器会停止，直到新的 Master 选举产生后，计时器才会继续计时，也就是说，从旧的 Master 崩溃到新的 Master 选举产生所花费的时间将不计入会话超时的计算中，这等价于延长了客户端的会话租期。

如果新的Master在短时间内就选举产生了，那么客户端就可以在本地会话租期过期前与其创建连接。

而如果Master的选举花费了较长的时间，就会导致客户端只能清空本地的缓存，并进入宽限期进行等待。

从这里我们可以看出，由于宽限期的存在，使得会话能够很好地在服务端Master转换的过程中得到维持。

图3-2展示了一个完整的Chubby服务端Master故障恢复过程中所触发的所有事件序列。

在这整个故障恢复过程中，客户端必须使用宽限期来保证在Master转换过程完成之后，其会话依然有效。

在图3-2中，从左向右代表了时间轴的变化，使用横向粗箭头代表会话租期，并且在图中通过“M”和“C”来分别标记Master和客户端上的视图，例如“lease M1”和“lease C1”。斜向上的箭头代表了客户端向Master发出的KeepAlive请求，而斜向下的箭头则代表了Master反馈的KeepAlive响应。

从图3-2中我们可以看出，一开始在旧的Master服务器上维持了会话租期“lease M1”，在客户端上维持了对应的“lease C1”，同时客户端的KeepAlive请求1一直被Master阻塞着。

在一段时间之后，Master向客户端反馈了KeepAlive响应2，同时开始了新的会话租期“lease M2”，而客户端在接收到该KeepAlive响应之后，立即发送了新的KeepAlive请求3，并同时也开始了新的会话租期“lease C2”。至此，客户端和服务端Master之间的所有交互都是正常的。

但是随后，Master 发生了故障，从而无法反馈客户端的KeepAlive请求3。在这个过程中，客户端检测到会话租期“lease C2”已经过期，它会清空本地缓存，并进入宽限期。在这段时间内，客户端无法确定Master上的会话周期是否也已经过期，因此它不会销毁它的本地会话，而是将所有应用程序对它的API调用都阻塞住，以避免在这个期间进行的API调用导致数据不一致现象。

同时，在客户端宽限期开始时，Chubby客户端会向上层应用程序发送一个“jeopardy”事件。

一段时间之后，Chubby服务端选举产生了新的Master，并为该客户端初始化了新的会话租期“lease M3”。

当客户端向新的Master发送KeepAlive请求4时，Master检测到该客户端的Master周期号（Master epoch number）已经过期，因此会在KeepAlive响应5中拒绝这个客户端请求，并将最新的 Master 周期号发送给客户端。关于 Master 周期，将在后面的内容中做详细讲解。

之后，客户端会携带上新的Master周期号，再次发送KeepAlive请求6给Master，最终，整个客户端和服务端之间的会话就会再次恢复正常。

通过上面的详细介绍，不难看出，在Master转换的这段时间内，只要客户端的宽限期足够长，那么客户端应用程序就可以在没有任何察觉的情况下，实现Chubby的故障恢复，但如果客户端的宽限期设置得比较短，那么 Chubby 客户端就会丢弃当前会话，并将这个异常情况通知给上层应用程序。

一旦客户端与新的 Master 建立上连接之后，客户端和 Master 之间会通过互相配合来实现对故障的平滑恢复。

新的Master会设法将上一个Master服务器的内存状态构造出来。具体的，由于本地数据库记录了每个客户端的会话信息，以及其持有的锁和临时文件等信息，因此 Chubby 会通过读取本地磁盘上的数据来恢复一部分状态。

总的来讲，一个新的Chubby Master服务器选举产生之后，会进行如下几个主要处理。

1.新的Master选举产生后，首先需要确定Master周期。Master周期用来唯一标识一个Chubby集群的Master统治轮次，以便区分不同的Master。

一旦新的Master周期确定下来之后，Master就会拒绝所有携带其他Master周期编号的客户端请求，同时告知其最新的 Master周期编号，例如上述提到的 KeepAlive 请求 4。需要注意的一点是，只要发生Master重新选举，就一定会产生新的Master周期，即使是在选举前后Master都是同一台机器的情况下也是如此。

2.选举产生的新Master能够立即对客户端的Master寻址请求进行响应，但是不会立即开始处理客户端会话相关的请求操作。
3.Master根据本地数据库中存储的会话和锁信息，来构建服务器的内存状态。
4.到现在为止，Master 已经能够处理客户端的 KeepAlive 请求了，但依然无法处理其他会话相关的操作。
5.Master 会发送一个“Master 故障切换”事件给每一个会话，客户端接收到这个事件后，会清空它的本地缓存，并警告上层应用程序可能已经丢失了别的事件，之后再向Master反馈应答。
6.此时，Master会一直等待客户端的应答，直到每一个会话都应答了这个切换事件。
7.在Master接收到了所有客户端的应答之后，就能够开始处理所有的请求操作了。
8.如果客户端使用了一个在故障切换之前创建的句柄，Master 会重新为其创建这个句柄的内存对象，并执行调用。而如果该句柄在之前的Master周期中已经被关闭了，那么它就不能在这个Master周期内再次被重建了——这一机制就确保了即使由于网络原因使得Master接收到那些延迟或重发的网络数据包，也不会错误地重建一个已经关闭的句柄。

## Paxos协议实现

Chubby服务端的基本架构大致分为三层：

- 最底层是容错日志系统（Fault-Tolerant Log），通过Paxos算法能够保证集群中所有机器上的日志完全一致，同时具备较好的容错性。

- 日志层之上是Key-Value类型的容错数据库（Fault-Tolerant DB），其通过下层的日志来保证一致性和容错性。

- 存储层之上就是Chubby对外提供的分布式锁服务和小文件存储服务。

Paxos算法的作用就在于**保证集群内各个副本节点的日志能够保持一致**。

Chubby事务日志中的每一个Value对应Paxos算法中的一个Instance，由于Chubby需要对外提供不间断的服务，因此事务日志会无限增长，于是在整个Chubby运行过程中，会存在多个Paxos Instance。

同时，Chubby会为每一个Paxos Instance都按序分配一个全局唯一的Instance编号，并将其顺序写入到事务日志中去。

在多Paxos Instance的模式下，为了提升算法执行的性能，就必须选举出一个副本节点作为 Paxos 算法的主节点（以下简称 Master 或 Coordinator），以避免因为每一个 Paxos Instance都提出提案而陷入多个Paxos Round并存的情况。

同时，Paxos会保证在Master重启或出现故障而进行切换的时候，允许出现短暂的多个Master共存却不影响副本之间的一致性。

在Paxos中，每一个Paxos Instance都需要进行一轮或多轮“Prepare→Promise→Propose→Accept”这样完整的二阶段请求过程来完成对一个提案值的选定，而多个 Instance 之间是完全独立的，每个Instance可以自己决定每一个Round的序号，仅仅只需要保证在Instance 内部不会出现序号重复即可。

为了在保证正确性的前提下尽可能地提高算法运行性能，可以让多个Instance共用一套序号分配机制，并将“Prepare→Promise”合并为一个阶段，具体做法如下。

- 当某个副本节点通过选举成为 Master 后，就会使用新分配的编号 N 来广播一个Prepare 消息，该 Prepare 消息会被所有未达成一致的 Instance 和目前还未开始的Instance共用。

- 当Acceptor接收到Prepare消息后，必须对多个Instance同时做出回应，这通常可以通过将反馈信息封装在一个数据包中来实现。假设最多允许K个Instance同时进行提案值的选定，那么：

当前至多存在K个未达成一致的Instance，将这些未决的Instance各自最后接受的提案值（若该提案尚未接受任何值，则使用null来代替）封装进一个数据包，并作为Promise消息返回。

—同时，判断N是否大于当前Acceptor的highestPromisedNum值（当前已经接受的最大提案编号值），如果大于该值的话，那么就标记这些未决Instance和所有未来的 Instance 的 highestPromisedNum 值为 N——这样，这些未决 Instance和所有未来Instance都不能再接受任何编号小于N的提案。

· 然后 Master 就可以对所有未决 Instance 和所有未来 Instance 分别执行“Propose→Accept”阶段的处理。值得注意的是，如果当前Master能够一直稳定运行的话，那么在接下来的算法运行过程中，就不再需要进行“Prepare→Promise”的处理了。但是，一但Master发现Acceptor返回了一个Reject消息，说明集群中存在另一个Master，并且试图使用更大的提案编号（比如M，其M＞N）发送了Prepare消息。碰到这种情况，当前 Master 就需要重新分配新的提案编号（必须比 M 更大）并再次进行“Prepare→Promise”阶段的逻辑处理。

利用上述改进的Paxos算法，在Master稳定运行的情况下，只需要使用同一个编号来依次执行每一个Instance的“Promise→Accept”阶段逻辑处理。

在每个Instance的运行过程中，一旦接收到多数派的 Accept 反馈后，就可以将对应的提案值写入本地事务日志并广播COMMIT消息给集群中的其他副本节点，其他副本节点在接收到这个COMMIT消息之后也会将提案值写入到事务日志中去。如果某个副本节点因为宕机或者网络原因没有接收到 COMMIT 消息，可以主动向集群中的其他副本节点进行查询。

因此，我们可以看到，在 Chubby 的 Paxos 算法的实现中，只要维持集群中存在多数派的机器能够正常运行，即使其他机器在任意时刻发生宕机，也能保证已经提交的提案的安全性。

至此，我们已经实现了一套满足一致性的日志副本，在此基础上就可以在上层实现一个一致的状态机副本，这就是图3-3中的容错数据库（Fault-Tolerant DB）层。

在最初版本的Chubby中，使用了具有数据复制特性的Berkeley DB[6]（下文中我们简称“BDB”）来作为它的容错数据库。

BDB使用分布式一致性协议来进行集群中不同服务器之间数据库日志的复制。BDB 的底层实现采用了经典的 B 树数据结构，我们可以将其看作是一个能够存储大量数据的HashMap（映射）。

在Chubby的使用中，将每一个数据节点的节点路径名作为键，同时按照节点路径名进行排序，这样就能够使得兄弟节点在排序顺序中相邻，方便对数据节点的检索。

因此 Chubby 的设计变得非常简单，只需要在此基础上添加上Master租期特性即可。

但是在后来的开发维护过程中，Chubby的开发人员觉得使用BDB就会引入其他额外的风险和依赖，因此自己实现了一套更为简单的、基于日志预写和数据快照技术的底层数据复制组件，这样就大大简化了整个 Chubby 的系统架构和实现逻辑。在本书中，对于该容错数据库层在内存中的数据结构不做展开讨论，这里只对“数据快照和事务日志回放”机制的实现做一个简单讲解。

集群中的某台机器在宕机重启以后，为了恢复状态机的状态，最简单的方法就是将已经记录的所有事务日志重新执行一遍。但这会有一个明显的问题，就是如果机器上的事务日志已经积累了很多，那么恢复的时间就会非常长，因此需要定期对状态机数据做一个数据快照并将其存入磁盘，然后就可以将数据快照点之前的事务日志清除。

通常副本节点在进行宕机后的恢复过程中，会出现磁盘未损坏和损坏两种情况。

前者最为常见，一般通过磁盘上保存的数据库快照和事务日志就可以恢复到之前某个时间点的状态，之后再向集群中其他正常运行的副本节点索取宕机后缺失的部分数据变更记录，这样即可实现宕机后的数据恢复。另外一种则是磁盘损坏，无法直接从本地数据恢复的情况。针对这种异常情况，就需要从其他副本节点上索取全部的状态数据。

副本节点在完成宕机重启之后，为了安全起见，不会立即参与Paxos Instance流程，而是需要等待检测到K（K是允许并发的最大Instance数目）个Paxos Instance流程成功完成之后才能开始参与——这样就能够保证新分配的提案编号不会和自己以前发过的重复。

最后，为了提高整个集群的性能，还有一个改进之处在于：得益于Paxos算法的容错机制，只要任意时刻保证多数派的机器能够正常运行，那么在宕机瞬间未能真正写入到磁盘上（只有当真正调用操作系统Flush接口后，数据才能被真正写入物理磁盘中）的那一小部分事务日志也可以通过从其他正常运行的副本上复制来进行获取，因此不需要实时地进行事务日志的Flush操作，这可以极大地提高事务写入的效率。

通过本小节的介绍，相信读者对 Chubby 这一分布式锁服务已经有了一个比较全面的了解。

Chubby 并非是一个分布式一致性的学术研究，而是一个满足第 2 章中我们提到的各种一致性需求的工程实践，感兴趣的读者可以在其他资料中对其进行进一步的了解。

# 参考资料

《分布式一致性原理与实践》

* any list
{:toc}