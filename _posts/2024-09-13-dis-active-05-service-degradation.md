---
layout: post
title: 高可用篇之异地多活-05-服务降级
date: 2024-09-13 21:01:55 +0800
categories: [Distributed]
tags: [distributed, sh]
published: true
---

# 架构图

![](https://gitee.com/houbinbin/imgbed/raw/master/img/%E5%BC%82%E5%9C%B0%E5%8F%8C%E6%B4%BB-%E5%8D%95%E5%85%83%E5%8C%96.drawio.png)

# chat

## 详细介绍服务降级？what

服务降级是一种在分布式系统或微服务架构中用来提高系统稳定性和可用性的重要策略。

它的主要目标是当某个服务发生故障或性能下降时，自动采取措施限制影响范围，防止整个系统崩溃，从而为用户提供基本功能的最低限度的服务。

## 服务降级的概念

服务降级是指在系统压力过大或某个服务不可用的情况下，通过限制功能、降低服务质量、甚至暂时关闭某些非关键性功能，来保证核心功能的正常运行。

通过服务降级，系统可以在非常时期（如高流量、大规模攻击、内部错误等）牺牲部分用户体验，以维持整体的服务可用性。

## 服务降级的触发条件
服务降级通常在以下几种情况下被触发：
1. **系统负载过高**：系统达到或超过了其负载上限，影响性能。此时可以采取降级措施，关闭非核心服务，减少系统压力。
2. **依赖服务不可用**：系统中某个关键服务发生故障，可能导致整个系统的连锁反应。为了避免这种情况，可以主动降级依赖服务。
3. **网络异常**：由于网络问题导致服务之间的通信异常时，可以通过降级来提供部分可用的服务。
4. **第三方服务故障**：依赖的外部服务或第三方接口发生故障时，可以通过降级策略降低对这些服务的依赖。

## 服务降级的策略
服务降级的具体策略可以根据业务需求和系统架构来定制，常见的策略包括：

1. **返回默认值**：当依赖服务不可用时，可以返回预先设定的默认值，保证调用方业务逻辑的正常执行。例如，在电商系统中，如果商品推荐服务不可用，可以返回一组默认推荐。
2. **熔断机制**：结合熔断器，当某个服务的失败率超过预定阈值时，自动熔断该服务的调用，直接返回降级数据，以防止服务雪崩。
3. **限流**：限制系统的访问流量，优先保障核心业务功能。例如，限制登录、注册等非核心功能的调用频率，确保下单、支付等核心业务的可用性。
4. **功能限制**：在系统高峰期或出现异常时，暂时关闭部分非核心功能。例如，在大促活动期间，关闭某些非核心的推荐、评价等功能。
5. **降级页面**：在页面级别进行降级处理，例如当后端服务不可用时，前端直接展示一个降级页面或弹窗提示，告知用户部分功能暂时不可用。
6. **静态化**：当服务不可用时，返回静态数据或静态页面，以提供基本信息展示。

## 服务降级的实施方式
服务降级的实施方式可以通过代码逻辑、配置中心、熔断器等手段来实现。常见的技术栈包括：

1. **代码逻辑**：在代码中明确写入降级逻辑，例如使用 `try-catch` 来捕获异常，并在异常发生时执行降级逻辑。
2. **熔断器（Hystrix、Resilience4j）**：使用熔断器来自动监控服务的状态，并在服务出现问题时触发熔断，执行降级处理。
3. **配置中心（Spring Cloud Config、Nacos）**：通过配置中心动态调整服务降级策略，例如调整降级的阈值、启用或关闭降级。
4. **网关（API Gateway）**：在API网关层面进行服务降级，过滤或限制对后端服务的请求，保证系统的稳定性。

## 服务降级的最佳实践
1. **优先保障核心业务**：在进行服务降级时，首先要明确核心业务，并优先保障这些业务的可用性。
2. **预先设定降级策略**：在系统设计阶段，就应该考虑到可能的降级场景，制定详细的降级策略，并进行充分的测试。
3. **监控和报警**：对服务降级的触发条件进行实时监控，一旦触发降级，应有报警机制通知相关人员及时处理。
4. **自动化与手动相结合**：既要有自动化的降级机制，也要提供手动降级的入口，便于在特殊情况下人工干预。
5. **逐步恢复**：在服务恢复后，应逐步恢复降级的功能，避免流量突然增长导致二次故障。

服务降级是构建高可用分布式系统的关键手段，通过合理的降级策略，可以有效地避免单点故障和雪崩效应，提升系统的整体鲁棒性。

## 为什么需要服务降级？why

服务降级是一种在系统出现异常或高负载的情况下，通过降低系统功能和服务质量来确保核心功能的可用性和系统的稳定性的方法。

主要原因包括：

1. **保障系统的稳定性**：在高并发或部分服务不可用的情况下，如果继续以正常的服务标准运行，可能导致系统资源被迅速耗尽，甚至出现整个系统崩溃的情况。服务降级可以降低系统的压力，防止崩溃。

2. **保证核心功能可用**：通过降级非核心功能，系统可以集中资源来保证核心功能的正常运行。比如在电商网站中，在订单量激增时，可以临时关闭一些不太重要的功能（如推荐系统、评分系统），确保订单处理和支付功能的正常运行。

3. **提升用户体验**：虽然服务降级会使某些功能暂时不可用，但比起整个系统不可用，用户体验会更好。部分可用的系统通常比完全不可用更容易被用户接受。

4. **应对依赖服务的异常**：在分布式系统中，服务通常依赖其他微服务。如果某个依赖服务出现故障，直接向用户返回错误信息并不是最好的选择。服务降级可以在这种情况下提供一个替代方案，比如返回缓存数据或默认信息，从而提高系统的鲁棒性。

5. **降低服务故障传播风险**：通过隔离和降级异常服务，可以防止故障在系统中传播，从而影响到整个系统的其他部分。

## 服务降级有什么优缺点？适用的场景？

### 服务降级的优点

1. **提高系统稳定性**：服务降级通过在关键时刻降低非核心功能的优先级，确保系统的核心功能在高负载或异常情况下仍然可用，避免系统崩溃。

2. **提升用户体验**：在系统压力过大时，服务降级可以让用户至少使用部分功能，而不是直接返回错误。相比完全不可用，用户更容易接受部分功能不可用的情况。

3. **资源优化利用**：在资源有限的情况下，通过关闭或降低部分服务，可以将资源集中到最需要的地方，提高系统整体的性能和效率。

4. **隔离故障**：在分布式系统中，服务降级可以起到隔离故障的作用，防止某个服务的异常影响到整个系统的其他部分。

### 服务降级的缺点

1. **功能受限**：服务降级会降低系统的功能，某些用户操作可能暂时不可用，这可能导致用户不满或流失。

2. **增加系统复杂性**：实现服务降级需要额外的设计和开发工作，包括对不同降级策略的设计和降级流程的实现，这增加了系统的复杂性。

3. **可能隐藏问题**：频繁的服务降级可能掩盖系统存在的潜在问题，使得这些问题未被及时发现和解决，从而对系统的长期健康产生不利影响。

4. **需要权衡**：决定哪些服务需要降级、降级到什么程度，需要在用户体验和系统稳定性之间进行权衡，这可能会涉及复杂的决策过程。

### 适用的场景

1. **高并发场景**：在系统面临突发的高并发请求时，比如电商网站的促销活动、抢购活动，可以对一些非核心功能进行降级，确保支付、下单等核心功能的可用性。

2. **依赖服务异常**：在分布式系统中，如果某个依赖的微服务不可用或响应缓慢，可以对该服务进行降级处理，例如返回缓存数据或默认值，防止影响整体服务。

3. **系统异常或故障**：在部分系统模块出现异常或故障时，可以通过服务降级暂时关闭故障模块，避免影响其他模块的正常运行。

4. **资源紧张**：在系统资源（如 CPU、内存、数据库连接）紧张时，通过降级非核心功能，可以将资源集中在核心功能上。

5. **网络波动或延迟**：在网络状况不佳，导致部分服务响应缓慢时，可以通过服务降级来缓解问题，比如减少调用次数或切换到更简单的备选方案。

## 一般要如何优雅的实现服务降级？服务降级的最佳实践？

要优雅地实现服务降级，可以采用以下最佳实践和方法，确保在系统出现异常或高负载的情况下，降级策略能够平稳实施，并对用户体验产生最小的影响。

### 如何优雅地实现服务降级

1. **明确核心与非核心功能**：首先，需要对系统的功能进行分类，明确哪些是核心功能，哪些是非核心功能。核心功能在任何情况下都要尽量保证可用，而非核心功能可以考虑降级或关闭。

2. **设置降级策略**：根据不同场景，制定相应的降级策略。例如：
   - **静态数据替代**：在某些情况下，可以用静态数据、缓存数据或者默认值替代动态数据，比如在推荐系统不可用时，提供默认的热门商品列表。
   - **限流**：对非核心接口进行限流，限制其最大并发量，优先保证核心接口的请求。
   - **延迟处理**：将一些可以延迟处理的任务（如日志记录、统计分析）推迟到系统负载降低后再处理。
   - **功能开关**：使用配置中心或功能开关机制（如开关管理系统），在系统运行过程中随时开启或关闭某些功能。

3. **使用熔断器模式**：在微服务架构中，熔断器（Circuit Breaker）模式是一种常用的降级手段。例如，在调用第三方服务或其他微服务时，如果响应时间过长或出现异常，则自动触发熔断，直接返回降级处理结果，避免影响整个系统的性能。

4. **引入缓存**：在系统正常运行时，提前将一些非核心数据缓存起来。当服务发生异常时，可以直接从缓存中读取数据，减少对核心服务的依赖。

5. **异步调用**：对于非核心功能的操作，可以采用异步调用的方式。即使这些操作出现问题，也不会阻塞主流程，从而保证核心功能的正常执行。

6. **优雅的错误提示**：在前端或用户界面层面，为用户提供友好的错误提示或降级后的替代方案，而不是直接显示错误信息。这样可以缓解用户的负面情绪，提升用户体验。

7. **自动化监控和告警**：实时监控系统的运行状态，一旦发现异常，立即触发告警并执行降级操作。通过自动化的手段，可以快速应对突发状况，减少人工干预。

### 服务降级的最佳实践

1. **提前演练和测试**：在生产环境中部署服务降级策略之前，应该在测试环境中进行充分的演练，模拟各种异常场景，确保降级策略能够正常生效并达到预期效果。

2. **制定清晰的降级计划**：为每个服务制定清晰的降级计划，包括在什么情况下触发降级、降级的内容是什么、如何恢复等。这有助于在紧急情况下快速决策和执行。

3. **设置分级降级机制**：根据系统的不同压力情况，可以设置多级降级策略。例如，轻度降级时只关闭一部分功能，严重时关闭更多非核心功能，保证核心功能的最大可用性。

4. **关注恢复策略**：降级是暂时的措施，当系统恢复到正常状态后，应该有相应的策略逐步恢复被降级的服务，以确保系统能够恢复到完整的功能状态。

5. **使用容错框架**：使用成熟的容错框架，如 Netflix Hystrix（虽然已停止维护，但类似的思想可以被其他框架继承），可以方便地实现服务降级、熔断和限流等功能。

6. **数据监控与日志记录**：对每次降级事件进行监控和日志记录，包括降级的触发条件、持续时间、影响范围等，为后续分析和优化提供数据支持。

7. **逐步推出和回滚**：在实际生产环境中，可以采用灰度发布或逐步推出的策略，逐渐将降级策略应用于部分服务或用户，监控效果后再扩展到全局。同样，如果发现降级策略导致了问题，应具备快速回滚的机制。

### 典型场景示例

- **电商系统**：在大促期间，当订单系统压力过大时，关闭一些非核心功能，如用户评价、推荐算法，确保购物车、支付等核心功能的正常运行。
- **微服务架构**：在微服务架构中，如果某个服务不可用，可以通过熔断器返回默认值或备用方案，而不是等待该服务恢复，影响整体性能。
- **内容分发网络（CDN）**：在内容提供不可用或网络拥塞时，可以从 CDN 缓存中提供静态内容，保证用户访问体验。

## 为什么分布式服务已经保证了高可用了？还需要服务降级呢？

分布式服务确实在很多方面提升了系统的可用性和容错能力，但服务降级仍然是重要的。下面是几个原因，解释为什么即使在分布式系统中也需要服务降级：

### 1. **高可用性并不等于无限可用性**

- **资源有限**：即使是高可用的分布式系统，也有资源限制（如网络带宽、存储容量、计算能力）。在极端负载或资源争用的情况下，可能会出现性能瓶颈。
- **服务失败的可能性**：分布式系统虽然通过多节点和冗余设计提高了可用性，但单个服务或节点仍然可能出现故障，甚至可能影响到其他服务或节点。

### 2. **故障隔离和恢复**

- **局部故障处理**：服务降级可以帮助隔离局部故障，防止一个服务的失败影响整个系统。例如，某个服务的过载或故障可以通过降级策略降低负载，确保其他服务继续正常运行。
- **快速恢复**：通过降级策略，系统可以在出现问题时迅速转到备用方案，减少用户受到的影响，并在系统恢复正常后，逐步恢复降级的服务。

### 3. **用户体验**

- **稳定的用户体验**：即使在系统出现问题时，通过降级策略可以提供有限的功能，而不是完全失效。这种方式可以保持一定程度的用户体验，比起完全无法使用要好得多。
- **优先级管理**：服务降级允许系统根据业务需求优先保证核心功能的可用性，而对非核心功能进行降级，这样能更好地管理用户体验和业务需求之间的平衡。

### 4. **意外的负载峰值**

- **突发流量**：即使有高可用设计，也可能遇到意外的流量峰值，超出预期的负载压力。这种情况下，降级可以帮助控制负载，保护系统不被压垮。

### 5. **复杂性和风险**

- **系统复杂性**：分布式系统的复杂性增加了故障和性能问题的可能性。服务降级可以帮助应对这种复杂性带来的潜在风险，通过简单有效的策略来处理异常情况。
- **变化和部署**：系统更新、部署或配置变更可能引入新的风险或问题。服务降级可以作为一种保护机制，确保在变化过程中系统的稳定性。

### 总结

服务降级是一种策略，用于在系统出现问题或高负载时，通过降低某些功能的可用性来保护系统的整体稳定性和核心功能。

即使在分布式系统中，服务降级仍然是确保系统持续可用、用户体验良好和快速恢复的重要手段。

# 参考资料

https://www.51cto.com/article/781506.html

* any list
{:toc}