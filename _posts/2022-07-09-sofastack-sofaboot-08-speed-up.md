---
layout: post
title:  SOFABoot-08-启动加速
date:  2022-07-09 09:22:02 +0800
categories: [SOFA]
tags: [sofa, sofastack, sh]
published: true
---

## 前言

大家好，我是老马。

sofastack 其实出来很久了，第一次应该是在 2022 年左右开始关注，但是一直没有深入研究。

最近想学习一下 SOFA 对于生态的设计和思考。

## sofaboot 系列

[SOFABoot-00-sofaboot 概览](https://houbb.github.io/2022/07/09/sofastack-sofaboot-00-overview)

[SOFABoot-01-蚂蚁金服开源的 sofaboot 是什么黑科技？](https://houbb.github.io/2022/07/09/sofastack-sofaboot-01-intro)

[SOFABoot-02-模块化隔离方案](https://houbb.github.io/2022/07/09/sofastack-sofaboot-02-module-iosolation)

[SOFABoot-03-sofaboot 介绍](https://houbb.github.io/2022/07/09/sofastack-sofaboot-03-intro)

[SOFABoot-04-快速开始](https://houbb.github.io/2022/07/09/sofastack-sofaboot-04-quick-start)

[SOFABoot-05-依赖管理](https://houbb.github.io/2022/07/09/sofastack-sofaboot-05-depency-solve)

[SOFABoot-06-健康检查](https://houbb.github.io/2022/07/09/sofastack-sofaboot-06-health-check)

[SOFABoot-07-版本查看](https://houbb.github.io/2022/07/09/sofastack-sofaboot-07-version)

[SOFABoot-08-启动加速](https://houbb.github.io/2022/07/09/sofastack-sofaboot-08-speed-up)

[SOFABoot-09-模块隔离](https://houbb.github.io/2022/07/09/sofastack-sofaboot-09-module-isolation)

[SOFABoot-10-聊一聊 sofatboot 的十个问题](https://houbb.github.io/2022/07/09/sofastack-sofaboot-10-chat-10-q)

# 启动加速

SOFABoot 提供了模块并行加载以及 Spring Bean 异步初始化能力，用于加快应用启动速度。

模块并行加载参考相应文档，下面介绍如何使用 SOFABoot 异步初始化 Spring Bean 能力来提高应用启动速度。

# 引入依赖

SOFABoot 在 v2.6.0 开始提供异步初始化 Spring Bean 能力，引入如下 Starter 即可：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-starter</artifactId>
</dependency>
```

# 使用场景

在实际使用 Spring/Spring Boot 开发中，会有一些 Bean 在初始化过程中执行准备操作，如拉取远程配置、初始化数据源等等；

在应用启动期间，这类 Bean 会增加 Spring 上下文刷新时间，导致应用启动耗时变长。

为了加速应用启动，SOFABoot 通过配置可选项，将 Bean 的初始化方法(init-method) 使用单独线程异步执行，加快 Spring 上下文加载过程，提高应用启动速度。

# 使用方法

异步初始化 Bean 的原理是开启单独线程负责执行 Bean 的初始化方法(init-method)，因此在使用过程中，除了引入上述依赖管理，还需要在 Bean 的 xml 定义中配置 sofa:async-init="true" 属性，用于指定是否异步执行该 Bean 的初始化方法，例如：

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:sofa="http://sofastack.io/schema/sofaboot"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://sofastack.io/schema/sofaboot   http://sofastack.io/schema/sofaboot.xsd"
       default-autowire="byName">
    <!-- async init  test -->
    <bean id="testBean" class="com.alipay.sofa.runtime.beans.TimeWasteBean" init-method="init" sofa:async-init="true"/>
</beans>
```

# 配置

SOFABoot 异步初始化能力提供两个属性配置，用于指定负责异步执行 Bean 初始化方法(init-method)的线程池大小： 

+ com.alipay.sofa.boot.asyncInitBeanCoreSize > 线程池基本大小，默认值为 CPU 核数加一 + com.alipay.sofa.boot.asyncInitBeanMaxSize > 线程池中允许的最大线程数大小，默认值为 CPU 核数加一

配置可以通过 VM -D 参数或者 Spring Boot 配置文件 application.yml 设置。

# 小结

希望本文对你有所帮助，如果喜欢，欢迎点赞收藏转发一波。

我是老马，期待与你的下次相遇。

# 参考资料

https://www.sofastack.tech/projects/sofa-boot/speed-up-startup/

* any list
{:toc}