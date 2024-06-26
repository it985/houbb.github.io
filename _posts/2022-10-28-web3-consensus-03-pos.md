---
layout: post
title: web3 consensus 共识机制 权益证明 Proof of Stake/POS 代理持有量证明（代理权益证明 Delegated Proof of Stake/DPOS）
date: 2022-10-28 21:01:55 +0800
categories: [web3]
tags: [consensus, sh]
published: true
---

# 权益证明

持有量证明（英语：Proof of Stake或POS），又称权益证明，是加密货币中共识机制的一种。

# 实现原理

以共识算法的方式，使用伪随机数的方式指定持有货币的人为交易的验证者，并创造新的区块并接续在最长的链后面。

# 技术原理

随机选择

基于持有时间选择

# 加密货币的应用

依加密货币的设计，可以让获得担任验证者工作的人（即持有货币者）获得奖励以吸引更多的人参与。

此奖励通常会依照验证者持有的货币数量来计算，当持有的量越多获得的奖励也越高。

## 优点

和工作量证明相较之下，由于不需要矿工持续的进行挖矿产生区块记账，更能节省能源。

## 缺点

由于持有量越多时被指定到获得的奖励也越多，有可能会造成屯币的状况，降低货币的流通量。

# 代理权益证明

权益证明的问题是，大多数的持币人没有足够的专业知识或足够的预算，无法达到高性能节点所需的计算机硬件和软件要求，难以产生区块，因此持币量大的少数账户便能支配区块的生成，获取大部分的奖励。

代理持有量证明（又称代理权益证明，英语：Delegated Proof of Stake或DPOS）的出现旨在解决以上的问题。

DPOS与POS原理相同，主要分别在于每位持币人有权投票选出代理节点，由得票最多的若干节点负责生成区块。

DPOS引入了民主机制，持币量少的人亦能参与投票，决定之后能生成区块获取奖励的节点，以实现去中心化的目的。

## 优点

通过缩小选举节点的数量，能够在不增加计算资源的前提下有效减少网络压力。

## 缺点

选举固定数量的节点未必能完全实现去中心化；如参选的节点数量少或者投票人数低，选出的节点代点代表性不足。

# 推荐阅读

[DAG 有向无环图（Directed Acyclic Graph）](https://houbb.github.io/2020/01/23/data-struct-learn-03-dag)

[java 实现有向图(Direct Graph)](https://houbb.github.io/2020/01/23/data-struct-learn-03-direct-graph)

权威证明

空间证明

个人身份证明

# 参考资料

[POS](https://zh.wikipedia.org/wiki/%E6%8C%81%E6%9C%89%E9%87%8F%E8%AD%89%E6%98%8E)

* any list
{:toc}