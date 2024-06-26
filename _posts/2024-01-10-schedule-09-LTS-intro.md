---
layout: post
title: schedule-09-分布式任务调度框架 LTS light-task-scheduler 入门介绍
date: 2024-01-10 21:01:55 +0800
categories: [Schedule]
tags: [schedule, apache, distribued, work-flow, sh]
published: true
---

# LTS

[LTS(light-task-scheduler)](https://github.com/ltsopensource/light-task-scheduler) 主要用于解决分布式任务调度问题，支持实时任务，定时任务和Cron任务


> [lts.pdf](https://github.com/houbb/download/tree/master/download/pdf/lts/lts.pdf)


> 主要功能

1. 支持分布式，解决多点故障，支持动态扩容，容错重试等
2. Spring扩展支持，SpringBoot支持，Spring Quartz Cron任务的无缝接入支持
3. 节点监控支持，任务执行监控支持，JVM监控支持
4. 后台运维操作支持, 可以动态提交，更改，停止 任务

> 框架鸟瞰

LTS节点:

- JobClient：主要负责提交任务, 并接收任务执行反馈结果。
- JobTracker：负责接收并分配任务，任务调度。
- TaskTracker：负责执行任务，执行完反馈给JobTracker。
- LTS-Admin：（管理后台）主要负责节点管理，任务队列管理，监控管理等。

LTS支持任务类型：

- 实时任务：提交了之后立即就要执行的任务。
- 定时任务：在指定时间点执行的任务，譬如 今天3点执行（单次）。
- Cron任务：CronExpression，和quartz类似（但是不是使用quartz实现的）譬如 0 0/1 * * * ?

结构图

![lts](https://raw.githubusercontent.com/houbb/resource/master/img/zookeeper/lts/2016-09-27-lts-struct.png)

> 节点组

- 英文名称 NodeGroup,一个节点组等同于一个小的集群，同一个节点组中的各个节点是对等的，等效的，对外提供相同的服务。

- 每个节点组中都有一个master节点，这个master节点是由LTS动态选出来的，当一个master节点挂掉之后，LTS会立马选出另外一个master节点，
框架提供API监听接口给用户。

> FailStore

- 这个主要是用于失败了存储的，主要用于节点容错，当远程数据交互失败之后，存储在本地，等待远程通信恢复的时候，再将数据提交。
- FailStore主要用户JobClient的任务提交，TaskTracker的任务反馈，TaskTracker的业务日志传输的场景下。
- FailStore目前提供几种实现：leveldb,rocksdb,berkeleydb,mapdb,ltsdb，用于可以自由选择使用哪种,用户也可以采用SPI扩展使用自己的实现。

# chat

## 详细介绍一下 LTS light-task-scheduler

LTS（Light Task Scheduler）是一个轻量级的任务调度框架，用于在分布式系统中管理和调度任务。

它的设计目标是提供一个简单易用的任务调度解决方案，同时保持高性能和可靠性。

下面是对 LTS 的详细介绍：

### 特性：

1. **轻量级**：LTS 是一个轻量级的任务调度框架，设计简单，易于使用和部署。

2. **分布式任务调度**：LTS 支持分布式任务调度，可以在多台服务器上同时运行和管理任务。

3. **高可靠性**：LTS 具有高可靠性，支持任务的高可用性部署，能够自动处理任务执行失败和节点故障等情况。

4. **灵活的任务调度策略**：LTS 提供了丰富的任务调度策略，可以根据实际需求进行配置，包括定时调度、依赖关系调度等。

5. **作业分片**：LTS 支持将大型作业分片调度，可以将任务分解成多个子任务并行执行，提高任务执行效率。

6. **监控和管理**：LTS 提供了丰富的监控和管理功能，可以实时监控任务执行情况、节点状态等，并提供了管理界面和 API 接口方便管理任务。

7. **易于扩展**：LTS 的架构设计具有良好的扩展性，可以根据需求进行定制和扩展，支持添加自定义的任务调度插件。

### 架构：

LTS 的架构主要包括以下几个核心组件：

1. **Job Tracker（任务跟踪器）**：负责接收任务提交请求，进行任务调度和分配，管理任务的执行状态和进度。

2. **Job Queue（任务队列）**：用于存储待执行的任务，提供队列服务，支持任务的排队和优先级调度。

3. **Task Tracker（任务执行器）**：负责实际执行任务的节点，从任务队列中获取任务并执行，然后将执行结果反馈给任务跟踪器。

4. **Job Store（任务存储）**：用于存储任务的元数据信息，包括任务的定义、调度策略、执行状态等。

5. **Admin Console（管理控制台）**：提供任务的监控和管理功能，包括任务的提交、调度配置、执行日志查看等。

### 使用场景：

1. **定时任务调度**：适用于定时执行的任务，如数据备份、定时报表生成等。

2. **分布式计算任务**：适用于需要并行处理大数据集的任务，可以将任务分解成多个子任务并行执行。

3. **工作流调度**：适用于具有复杂依赖关系的任务调度场景，可以定义任务之间的依赖关系，实现工作流调度。

4. **实时数据处理**：适用于需要实时处理数据的场景，可以将任务按照事件驱动的方式调度和执行。

5. **异步任务处理**：适用于需要异步处理的任务，如异步消息处理、异步通知等。

总的来说，LTS 是一个功能丰富、性能高效、易于使用的任务调度框架，适用于各种分布式系统中的任务调度和管理需求。

* any list
{:toc}