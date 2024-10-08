---
layout: post
title: X-Pipe 是由携程框架部门研发的Redis多数据中心复制管理系统。
date: 2024-09-19 21:01:55 +0800
categories: [Distributed]
tags: [distributed, sh]
published: true
---

# About

X-Pipe是由携程框架部门研发的Redis多数据中心复制管理系统。

基于Redis的Master-Slave复制协议，实现低延时、高可用的Redis多数据中心、跨公网数据复制，并且提供一键机房切换，复制监控、异常报警等功能。

开源版本和携程内部生产环境版本一致。

# XPipe 解决什么问题

Redis 在携程内部得到了广泛的使用，根据客户端数据统计，整个携程全部 Redis 的读写请求在每秒 2000W，其中写请求约 100W，很多业务甚至会将 Redis 当成持久化的内存数据库使用。

这样，就对 Redis 多数据中心提出了很大的需求，一是为了提升可用性，解决数据中心 DR(Disaster Recovery) 问题，二是提升访问性能，每个数据中心可以读取当前数据中心的数据，无需跨机房读数据，在这样的需求下，XPipe 应运而生 。

为了方便描述，后面用 DC 代表数据中心 (Data Center)。

# 系统详述

## 整体架构

整体架构图如下所示：

![整体架构](https://camo.githubusercontent.com/d6e64fed203a35baaf9037f8b28bc1629e2ef8bb981d18df6746f0bcc9b4cf17/68747470733a2f2f7261772e6769746875622e636f6d2f6374726970636f72702f782d706970652f6d61737465722f646f632f696d6167652f746f74616c2e6a7067)

Console 用来管理多机房的元信息数据，同时提供用户界面，供用户进行配置和 DR 切换等操作。

Keeper 负责缓存 Redis 操作日志，并对跨机房传输进行压缩、加密等处理。

Meta Server 管理单机房内的所有 keeper 状态，并对异常状态进行纠正。

## Redis 数据复制问题

多数据中心首先要解决的是数据复制问题，即数据如何从一个 DC 传输到另外一个 DC。

我们决定采用伪 slave 的方案，即实现 Redis 协议，伪装成为 Redis slave，让 Redis master 推送数据至伪 slave。

这个伪 slave，我们把它称为 keeper，如下图所示：

![slave](https://camo.githubusercontent.com/5b4af3c62d97379150fb747d0a47842820ecd017fe89cbcd17df9d50eb5503ec/68747470733a2f2f7261772e6769746875622e636f6d2f6374726970636f72702f782d706970652f6d61737465722f646f632f696d6167652f6b6565706572732e6a7067)

使用 keeper 带来的优势

- 减少 master 全量同步

如果异地机房 slave 直接连向 master，多个 slave 会导致 master 多次全量同步，而 keeper 可以缓存 rdb 和 replication log，异地机房的 slave 直接从 keeper 获取数据，增强 master 的稳定性。

- 减少多数据中心网络流量

在两个数据中心之间，数据只需要通过 keeper 传输一次，且 keeper 之间传输协议可以自定义，方便支持压缩 (目前暂未支持)。

- 网络异常时减少全量同步

keeper 将 Redis 日志数据缓存到磁盘，这样，可以缓存大量的日志数据 (Redis 将数据缓存到内存 ring buffer，容量有限)，当数据中心之间的网络出现较长时间异常时仍然可以续传日志数据。

- 安全性提升

多个机房之间的数据传输往往需要通过公网进行，这样数据的安全性变得极为重要，keeper 之间的数据传输也可以加密 (暂未实现)，提升安全性。

## 机房切换

### 切换流程

检查是否可以进行 DR 切换

类似于 2PC 协议，首先进行 prepare，保证流程能顺利进行。

原主机房 master 禁止写入

此步骤，保证在迁移的过程中，只有一个 master，解决在迁移过程中可能存在的数据丢失情况。

提升新主机房 master

其它机房向新主机房同步

同时提供回滚和重试功能。回滚功能可以回滚到初始的状态，重试功能可以在 DBA 人工介入的前提下，修复异常条件，继续进行切换。



# 高可用

## XPipe 系统高可用

如果 keeper 挂掉，多数据中心之间的数据传输可能会中断，为了解决这个问题，keeper 有主备两个节点，备节点实时从主节点复制数据，当主节点挂掉后，备节点会被提升为主节点，代替主节点进行服务。

提升的操作需要通过第三方节点进行，我们把它称之为 MetaServer，主要负责 keeper 状态的转化以及机房内部元信息的存储。同时 MetaServer 也要做到高可用：每个 MetaServer 负责特定的 Redis 集群，当有 MetaServer 节点挂掉时，其负责的 Redis 集群将由其它节点接替；如果整个集群中有新的节点接入，则会自动进行一次负载均衡，将部分集群移交到此新节点。

## Redis 自身高可用

Redis 也可能会挂，Redis 本身提供哨兵 (Sentinel) 机制保证集群的高可用。但是在 Redis4.0 版本之前，提升新的 master 后，其它节点连到此节点后都会进行全量同步，全量同步时，slave 会处于不可用状态；master 将会导出 rdb，降低 master 的可用性；同时由于集群中有大量数据 (RDB) 传输，将会导致整体系统的不稳定。

截止当前文章书写之时，4.0 仍然没有发布 release 版本，而且携程内部使用的 Redis 版本为 2.8.19，如果升到 4.0，版本跨度太大，基于此，我们在 Redis3.0.7 的版本基础上进行优化，实现了 psync2.0 协议，实现了增量同步。下面是 Redis 作者对协议的介绍：psync2.0。

# 参考资料

https://github.com/ctripcorp/x-pipe

* any list
{:toc}