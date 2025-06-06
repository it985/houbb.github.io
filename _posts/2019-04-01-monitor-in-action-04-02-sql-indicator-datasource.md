---
layout: post
title: 监控系统实战-04-02-SQL 指标的数据源从哪里来？
date: 2018-11-25 09:14:43 +0800
categories: [Devops]
tags: [devops, monitor, apm, zabbix, sh]
published: true
---

# 背景

我们在配置数据源的时候，希望可以默认加载好一些内置的数据源。

一个是为了方便，一个是为了安全。

安全层面可以是避免密码的泄露，或者是用户手动连接一些业务主库之类的。

如果我们可以控制这些，那么就会方便很多。


# v1-用户自己填写

## 流程

最简单的思路就是我们让用户可以提前配置一些数据源。

这个数据源隶属于某一个业务域之类的。这个限制信息可选。

如果想控制，可以在数据层加一层限制，比如用户的业务域下的已有数据源之类的。

## 安全性

除却加密的安全性。

可以考虑在用户引用数据源的时候，添加审核审批流。

可以将这个审批流内置到系统中，避免缺少审批流系统。

# v2-cmdb 维护

## 人工维护

一个是让用户自己在 cmdb 维护，但是这个时间长了肯定存在各种问题

## 配置扫描

如果系统存在 OPS 配置系统，且所有的访问都是标准的，这也是一种思路。

但是也存在问题，可能配置更新不及时，或者内置写死等等问题。

## port 扫描

也可以通过定时巡检，扫描全部应用机器上的机器端口号等信息，结合 database 的基本信息，维护实时的关系。

缺点是需要依赖巡检作业+database的基本数据。


# 小结

cmdb 的维护需要花费大量的精力。

初期可以使用让用户自己维护的方式，做好对应的安全控制即可。

# 参考资料

无

* any list
{:toc}