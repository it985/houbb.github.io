---
layout: post
title: web3 以太坊开发-02-术语
date: 2022-10-28 21:01:55 +0800
categories: [web3]
tags: [web3, dev, ethereum, sh]
published: true
---


# 区块链

所有在以太坊网络历史上提交给以太坊网络的区块的序列。 

如此命名，是因为每个区块都包含对前一个区块的引用，这有助于保持所有区块的顺序，因而维持了精确历史记录的顺序。

# ETH 以太币

以太币 (ETH) 是以太坊上的原生加密货币。 

用户向其他用户支付以太币，让他们完成自己的代码执行请求

# EVM 以太坊虚拟机

以太坊虚拟机是一个全局虚拟计算机，以太坊网络中的每个参与者都会存储并一致同意其状态。 

任何参与者都可以请求在以太坊虚拟机上执行任意代码；代码执行会改变以太坊虚拟机的状态。

# Nodes 节点

存储以太坊虚拟机状态的实体计算机。 

节点间相互通信，传播关于以太坊状态的信息及其新的状态变化。 

任何用户还可以通过广播来自节点的代码执行请求来请求代码执行。 

以太坊网络本身就是所有以太坊节点及其通信的集合。

# 帐户

帐户是存储以太币之处。 

用户可以初始化帐户，将以太币存入帐户，并将自己帐户中的以太币转账给其他用户。 

帐户和帐户余额存储在以太坊虚拟机中的一个大表格中，是以太坊虚拟机总体状态的一部分。

# 交易

“交易请求”是表示以太坊虚拟机上的代码执行请求的正式术语。

“交易”是指已完成的交易请求和相关的以太坊虚拟机状态变化。 

任何用户都可以从节点向网络广播交易请求。 

为了使交易请求影响一致同意的以太坊虚拟机状态，就必须由其他节点对其进行验证、执行并“提交到网络”。 

执行任何代码都会导致以太坊虚拟机状态变化；一旦提交后，该状态变化将广播到网络中的所有节点。 

以下为一些交易示例：

```
从我的帐户发送 X 个以太币到 Alice 的帐户。
将一些智能合约代码发布到以太坊虚拟机状态中。
使用 Y 参数执行 EVM 中 X 地址的智能合约代码。
```

# 区块

交易量巨大，因此交易分批或分区块“提交”。 

区块通常包含数十至数百笔交易。

# 智能合约

是指开发者发布到以太坊虚拟机状态中的可重用代码片段（程序）。 

任何人都可以通过提出交易请求来请求执行智能合约代码。 

因为开发者可以通过发布智能合约将任意可执行应用程序（游戏，市场，金融工具等）写入以太坊虚拟机，所以这些通常也称为 dapp 或去中心化应用程序。


# 参考资料

https://ethereum.org/zh/developers/docs/intro-to-ethereum/#terminology


* any list
{:toc}