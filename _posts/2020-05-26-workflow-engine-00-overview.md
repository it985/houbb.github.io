---
layout: post
title: 工作流引擎-00-流程引擎（Process Engine）概览 overview
date:  2020-5-26 16:05:35 +0800
categories: [Engine]
tags: [engine, workflow-engine, workflow, bpm, flow]
published: true
---


# 前言

大家好，我是老马。

最近想设计一款审批系统，于是了解一下关于流程引擎的知识。

下面是一些的流程引擎相关资料。

## 工作流引擎系列

[工作流引擎-00-流程引擎概览](https://houbb.github.io/2020/05/26/workflow-engine-00-overview)

[工作流引擎-01-Activiti 是领先的轻量级、以 Java 为中心的开源 BPMN 引擎，支持现实世界的流程自动化需求](https://houbb.github.io/2020/05/26/workflow-engine-01-activiti)

[工作流引擎-02-BPM OA ERP 区别和联系](https://houbb.github.io/2020/05/26/workflow-engine-02-bpm-oa-erp)

[工作流引擎-03-聊一聊流程引擎](https://houbb.github.io/2020/05/26/workflow-engine-03-chat-what-is-flow)

[工作流引擎-04-流程引擎 activiti 优秀开源项目](https://houbb.github.io/2020/05/26/workflow-engine-04-activiti-opensource)

[工作流引擎-05-流程引擎 Camunda 8 协调跨人、系统和设备的复杂业务流程](https://houbb.github.io/2020/05/26/workflow-engine-05-camunda-intro)

[工作流引擎-06-流程引擎 Flowable、Activiti 与 Camunda 全维度对比分析](https://houbb.github.io/2020/05/26/workflow-engine-06-compare)

[工作流引擎-07-流程引擎 flowable-engine 入门介绍](https://houbb.github.io/2020/05/26/workflow-engine-07-flowable-intro)

[工作流引擎-08-流程引擎 flowable-engine 优秀开源项目](https://houbb.github.io/2020/05/26/workflow-engine-08-flowable-opensource)

[工作流引擎-09-XState 是一个 JavaScript 和 TypeScript 的状态管理库，它使用状态机和状态图来建模逻辑](https://houbb.github.io/2020/05/26/workflow-engine-09-xstate-intro)

[工作流引擎-10-什么是 BPM?](https://houbb.github.io/2020/05/26/workflow-engine-10-bpm-intro)

[工作流引擎-11-开源 BPM 项目 jbpm](https://houbb.github.io/2020/05/26/workflow-engine-11-opensource-bpm-jbpm-intro)

[工作流引擎-12-开源 BPM 项目 foxbpm](https://houbb.github.io/2020/05/26/workflow-engine-12-opensource-bpm-foxbpm-intro)

[工作流引擎-13-开源 BPM 项目 UFLO2](https://houbb.github.io/2020/05/26/workflow-engine-13-opensource-bpm-uflo-intro)

[工作流引擎-14-开源审批流项目之 RuoYi-vue + flowable 6.7.2 的工作流管理](https://houbb.github.io/2020/05/26/workflow-engine-14-opensource-ruoyi-flowable-intro)

[工作流引擎-15-开源审批流项目之 RuoYi-Vue-Plus 进行二次开发扩展Flowable工作流功能](https://houbb.github.io/2020/05/26/workflow-engine-15-opensource-ruoyi-flowable-plus-intro)

[工作流引擎-16-开源审批流项目之 整合Flowable官方的Rest包](https://houbb.github.io/2020/05/26/workflow-engine-16-opensource-flowable-ui-intro)

[工作流引擎-17-开源审批流项目之 flowable workflow designer based on vue and bpmn.io](https://houbb.github.io/2020/05/26/workflow-engine-17-opensource-workflow-bpmn-modeler-intro)

[工作流引擎-18-开源审批流项目之 plumdo-work 工作流，表单，报表结合的多模块系统](https://houbb.github.io/2020/05/26/workflow-engine-18-opensource-plumdo-work-intro)


# 到底什么是工作流？

工作流最早起源于生产组织和办公自动化领域，它是针对平时工作中的业务流程活动而提出的一个概念，目的是根据将工作分解成定义良好的任务或角色，根据一定的原则和过程来实施这些任务并加以监控，从而达到提高效率、控制过程、提升客户服务、增强有效管理业务流程等目的。

为了更好地实现某些业务工作目标，可以利用计算机在很多个参与人之间按某种既定原则自动传递文档、信息内容或者任务。

因此，**只要信息在人与人、人与系统或者系统与系统之间进行传递，就必须构建工作流**。

# 工作流类型有哪些

工作流是在整个工作区中发生的，有些是结构化的，有些是非结构化的。

当数据从一个任务转移到另一个任务时，工作流就存在了。

但是，如果数据没有流动，就没有工作流。

比如遛狗、去杂货店和取干洗衣物等，这都不是工作流，而是任务管理。

## 1. 流程工作流（Process Workflow）

当一组任务具有可预测性和重复性时，就会发生流程工作流。也就是说，在项目开始工作流之前，您已明确数据的流转方向。

比如采购申请批准工作流，一旦申请提交，每一步处理工作相对固定，工作流几乎不会有变化。

## 2. 项目工作流（Project Workflow）

项目具有类似于流程的结构化路径，但在此过程中可能具有更大的灵活性，项目工作流只适用于一个项目。

比如发布一个新版本的网站，你可以准确预测项目的任务流程，但是这个任务流程不适用于另一个网站的发布。

## 3.案例工作流（Case Workflow）

在案例工作流中，对于数据流转的方向是不明确的。只有收集到大量的数据时，数据流转的方向才会比较明显。

比如保险索赔，一开始并不知道如何处理，只有经过一番调查，才会明确。

## 什么情况下，需要使用工作流引擎？

通过前面的解释，我们对工作流以及工作流的类型有了基本的了解。

而题主提到的工作流引擎是业务流程管理系统的一部分，它为业务流程的管理系统提供了根据角色、分工和条件等不同决定信息的流转处理规则和路径。

工作流引擎包括流程的节点管理、流程分支流向管理等重要功能。

# 工作流自动化的好处

## 1. 消除冗余的任务提高效率

进行信息收集，可以舍弃使用Excel进行填写，并可以自动整理数据。

通过进行数据流转，可以舍弃使用电子邮件进行交流。

最后，通过轻流进行数据分析，可以舍弃手动创建数据报表。三个环节都可以消除冗余的任务，并提高效率。

## 2. 更高的可视化程度

强大的报表功能可以让你快速分析数据情况，清晰的跟进项目的实施情况。

## 3.明确各节点负责人的责任

无论采用哪种工作流类型，都可以将工作分配到每个节点负责人手里，而且每一步操作内容都可以通过流程日志进行查看，方便后期责任的确认和追踪。

# 工作流引擎

所谓工作流引擎，是指workflow作为应用系统的一部分，并为之提供对各应用系统有决定作用的根据角色、分工和条件的不同决定信息传递路由、内容等级等核心解决方案。

工作流引擎包括流程的节点管理、流向管理、流程样例管理等重要功能。

## 简介

开发一个优秀的软件系统，系统界面是最基础的部分，数据库之间的信息交换是必备条件，而根据业务需求开发出符合实际的程序逻辑，并在一定程度上保证其稳定性、易维护性才是根本。

稳定性自不必说，易维护性则要保证模块化和结构化，这样可以在业务流程发生变化，例如决策权的改变、组织结构的变动时产生的全新业务逻辑，而工作流引擎解决的就是这个问题。如果应用程序缺乏强大的逻辑层，就会变得容易出错，比如信息的路由错误、死循环等等。

举个简单的例子，一辆汽车，外观很漂亮，但是如果发动机有问题，那就变成了一个摆设，势必会bug不断。而应用系统的拓展性就好比汽车的引擎转速，别人的百公里加速只要10s，而你的则需要一个小时（业务流程变动需要更长时间的程序修改），孰优孰劣，一目了然。而如果引擎再动不动就熄火（程序逻辑死循环），那这样的车谁还会叫好呢？

## 服务架构

面向服务的体系结构，是一个组件模型，它将应用程序的不同功能单元通过这些服务之间定义良好的接口和契约联系起来。

接口是采用中立的方式进行定义的，它应该独立于实现服务的硬件平台、操作系统和编程语言。

工作流引擎使得构建在各种这样的系统中的服务，可以以一种统一和通用的方式进行交互。

## 整体思路

首先定义每个操做，就是定义流程步，定义流程步主要包括：操作的接口地址、操作参数、操作类型（起始操作、中间操作等）。定义操作的目的是接着为每个操作设置关系和定义流程时选用这些定义好的操作步。

第二定义操作的参数，有了接口地址外，还需要定义操作参数。

第三是定义操作步之间的关系。就是定义一个流程中每个操作步的前驱、后继的操作步。

第四是定义流程了，必要的信息是流程名称等基本信息和定义流程的各个操作步以及流转规则。流程基本信息就不用说了。流程步定义比较复杂，设置定义步骤类型（起始、中间、终结），入口步骤、出口步骤、通知模式、人员、角色、发送通知的内容。

第五是涉及跳步情况的定义，比如需要根据参数的不同提交到不同的步骤进行审批，这里叫做流程步骤变迁规则设置。设置的内容需要：原步骤、目标步骤、变迁方向（正／负）、条件规则（判断参数时用与还是用或）。接着设置参数和参数值及比较条件。

第六是授权管理。这个比较简单了，判断被提交的人是否处于授权状态，从而进行授权处理。

以上所说只是基础逻辑，实际编写过程中还要考虑各企业的实际情况。

# 工作流引擎对比

## 对比

几种工作流引擎对比：

1、jBPM3是一个完整的工作流系统实现，面向开发人员，目的在于简化对组织核心流程进行支撑的软件创建，不支持标准。

2、jBPM4引入PVM，使其拥有更强大的扩展性，同时增加BPMS特性，这些特性包括了对BPMN的支持、面向业务人员的Web建模器和简单统计分析功能的加入。

3、jBPM5基于原先的Drools Flow，支持BPMN，通过与Drools的合并支持BAM，通过内容仓库增加对流程可视化的支持。由于放弃了jBPM4的PVM，引擎的可扩展性受到损害，并且不再支持jPDL。

4、Activiti5基于jBPM4的开源工作流系统，与Alfresco的集成增加了其流程可视化与管理能力，同时通过创新的Activiti Cycle协作组件支持流程相关人员之间的协调，最后，它加强了集成能力。

5、SWF与其说是工作流引擎，不如说是分布式计算调度框架，SWF中只包括Task和History两部分，甚至是每个Task之间如果要传递一些数据的话，都只能通过第三方存储（比如Message Queue或者Redis），不过这也给了编程更大的灵活性，问题是这种灵活性是不是非常需要。

一个SWF由Worker和Decider组成，Worker执行实际的任务，而Decider进行流程控制，两者严格上来讲没有区别，只是所执行的任务不同罢了。每个Worker和Decider会定期的去SWF的一个Task List取下一个任务。可以看出来这更像是一个“多线程”的结构，而SWF官方网站的Use Case是NASA的火星探索计划中需要处理图片的系统，这其实也是一个更多侧重于计算的系统，流程反而非常简单。

另外，SWF（Simple Workflow）的一个Workflow不能太复杂，因为所有的流程控制都集中于Decider，如果太复杂的话Decider将无比庞大，给维护和扩展带来一定的困扰。



# 个人收获

- 授权

# 拓展阅读 

BPM

OA

ERP

CRM

# 参考资料

[java开源工作流引擎梳理](https://blog.csdn.net/u010850285/article/details/41246093)

[什么情况下，需要使用工作流引擎？](https://www.zhihu.com/question/20857512)

[什么是工作流引擎](https://www.jianshu.com/p/2bca30daa68c)

[java spring actviti 工作流引擎 前后端分离 框架源码](https://www.jianshu.com/p/a61d225bbd97)

[Java三大主流开源工作流引擎技术分析](https://www.cnblogs.com/yuhuameng/p/5203055.html)

[几大工作流引擎对比](https://www.cnblogs.com/jpfss/p/11075458.html)

* any list
{:toc}