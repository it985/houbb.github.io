---
layout: post
title: 加密货币混币器
date: 2022-10-28 21:01:55 +0800
categories: [web3]
tags: [distributed-computing, sh]
published: true
---

# 分叉

在密码货币中，分叉含有数个定义：

- 一条区块链分开成两条区块链

- 一个协议的改变或两个区块拥有相同的高度

为了维持货币过去的交易纪录，密码货币一般进行分叉以增加新功能或堵塞漏洞。

# 类型

分叉可分为意外分叉和有意分叉。

当两个或以上的矿工在几乎相同的时间成功挖到区块，便会出现意外分叉。 

此时，矿工便会分别在两条分叉上各自挖矿，直至其中一条分叉比其他分叉更长。

（这代表矿工对采纳哪一个分叉已达成共识）因此，矿工网络随后便会放弃挖掘其他分叉。被抛弃的区块被称为孤立区块。

因此，不少密码货币使用者，均要求交易需要多次确认，以防止意外分叉使交易所在之区块变为无效。

有意分叉则对原区块链作出修改，可再分类如下：

## 硬分叉

硬分叉之中新分叉所产生之区块将被旧软件视为无效。

因此所有参与者，包括交易服务器以及矿工（节点），都必须更新软件，才能继续运行新分叉。[4]如有节点组继续使用旧软件，而其他节点使用新的软件，便有可能产生分裂成两只货币。

## 软分叉

与硬分叉相比，软分叉所产生之区块能够被旧软件识别为有效区块，即区块 向下兼容。

然而，旧软件所产生之区块则未必在新规则下有效。

# 推荐阅读

[DAG 有向无环图（Directed Acyclic Graph）](https://houbb.github.io/2020/01/23/data-struct-learn-03-dag)

[java 实现有向图(Direct Graph)](https://houbb.github.io/2020/01/23/data-struct-learn-03-direct-graph)

# 参考资料

[分叉](https://zh.wikipedia.org/zh-cn/%E5%88%86%E5%8F%89)

* any list
{:toc}