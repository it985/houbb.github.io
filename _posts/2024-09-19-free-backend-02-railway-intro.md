---
layout: post
title: 免费的后端服务器？railway 免费已取消
date: 2024-09-19 21:01:55 +0800
categories: [Cloud]
tags: [cloud, deploy, sh]
published: true
---

# chat

## 详细介绍一下 railway 

Railway 是一个旨在简化开发者工作流程的云平台，它提供了一种无缝的方式来构建、部署和管理应用程序。

下面是 Railway 的一些关键特点和功能：

### 1. **平台即服务 (PaaS)**
Railway 是一种平台即服务，它允许开发者将应用程序和数据库托管在云端，而无需处理底层基础设施的复杂性。

### 2. **自动化部署**
Railway 提供了自动化的部署流程，使得代码提交和更新能自动部署到生产环境。它集成了 GitHub 等版本控制系统，使得部署变得简单而高效。

### 3. **多语言支持**
Railway 支持多种编程语言和框架，包括 Node.js、Python、Ruby、Go、Java 等。这使得开发者可以使用他们熟悉的工具和技术栈来构建应用程序。

### 4. **一键连接数据库**
Railway 提供了对多种数据库的支持，如 PostgreSQL、MySQL、MongoDB 等。通过简单的配置，开发者可以快速将数据库与他们的应用程序连接。

### 5. **环境变量管理**
Railway 提供了方便的环境变量管理功能，允许开发者在不同的环境（如开发、测试、生产）中配置和管理变量。

### 6. **可视化仪表盘**
Railway 提供了一个直观的仪表盘，开发者可以通过它监控应用程序的性能、查看日志、管理环境变量等。

### 7. **集成和插件**
Railway 支持通过集成和插件来扩展其功能。例如，开发者可以集成 CI/CD 工具、监控工具等，以满足特定需求。

### 8. **简单的定价模型**
Railway 提供了一种简单的定价模型，通常基于使用的资源量进行计费，这让开发者可以根据实际使用情况控制成本。

### 9. **社区和支持**
Railway 拥有一个活跃的社区，开发者可以在社区中获得支持、分享经验以及讨论最佳实践。

### 使用案例
- **开发和测试环境**: 快速设置和管理开发和测试环境。
- **生产部署**: 将应用程序无缝地部署到生产环境。
- **原型设计**: 迅速构建和迭代原型应用程序。

总的来说，Railway 的目标是降低开发和部署应用程序的复杂性，使开发者能够专注于构建应用程序，而不是管理基础设施。

## railway 免费吗？

Railway 提供了免费层，但它也有一些付费选项。免费层通常包括一定量的资源和功能，适合个人开发者、初创公司或者小型项目。免费层的限制可能包括：

- **资源限制**: 如计算资源、存储、带宽等。
- **服务等级**: 可能没有高级功能或支持。
- **环境变量和数据库连接数**: 在免费层中可能会有一定限制。

对于需要更多资源、功能或企业级支持的用户，Railway 提供了多种付费计划。这些计划通常包括：

- **更多的计算资源**: 处理更高的负载。
- **更多的存储和带宽**: 适合大型应用程序。
- **优先支持和服务**: 更快的响应时间和更多的支持选项。

你可以访问 Railway 的官网查看详细的定价信息和免费层的具体限制，以及比较不同计划的功能和价格。

## 免费方案

## 定价方案

Railway 的定价方案旨在支持您无论以何种方式进行工作。我们收取您所选择方案的基本订阅费用，以及额外的资源和附加服务费用。

### 计划

Railway 提供三种计划，另外还有一个免费试用：

- **Hobby**：适用于独立开发者和开发人员构建和部署个人项目
- **Pro**：适用于专业开发者及其团队进行生产环境的交付
- **Enterprise**：适用于需要合规性、服务水平协议（SLA）和账户管理等企业功能的团队

### 订阅定价

每个 Railway 账户都需要一个有效的订阅。基本订阅费用允许您使用 Railway 平台及其所在订阅级别所包含的功能。

| 计划       | 价格                        |
|------------|-----------------------------|
| Hobby      | $5 / 月                     |
| Pro        | $20 / 每位团队成员 / 月     |
| Enterprise | 定制                         |

有关更多计划信息，请访问 [railway.app/pricing](https://railway.app/pricing)。

### 默认计划资源

根据您所使用的计划，您可以在每项服务中使用以下资源。

| 计划       | 内存 (RAM) | CPU  | 临时存储   | 卷存储   |
|------------|------------|------|------------|----------|
| Trial      | 0.5 GB     | 2 vCPU| 1 GB       | 0.5 GB   |
| Hobby      | 8 GB       | 8 vCPU| 100 GB     | 5 GB     |
| Pro        | 32 GB      | 32 vCPU| 100 GB    | 50 GB    |
| Enterprise | 64 GB      | 64 vCPU| 100 GB    | 50 GB    |

请注意，这些是初始值，Pro 计划及以上的用户可以申请增加限制。

Pro 用户及以上可以自行增加卷存储最多至 250 GB。

### 资源使用定价

在上述基本订阅费用之外，Railway 还会对您使用的资源进行收费。

您仅为实际使用的资源付费，这有助于防止云费用失控，并确保您始终以最佳价格获得云支出。

| 资源         | 资源价格                     |
|--------------|------------------------------|
| 内存 (RAM)    | $10 / GB / 月 ($0.000231 / GB / 分钟) |
| CPU           | $20 / vCPU / 月 ($0.000463 / vCPU / 分钟) |
| 网络出口      | $0.10 / GB ($0.000000095367432 / KB) ― 从 2023 年 8 月 1 日起 |
| （可选附加）卷存储 | $0.25 / GB / 月 ($0.000005787037037 / GB / 分钟) |

有关控制资源使用成本的更多信息，请阅读我们的 FAQ [如何防止超支？](https://railway.app/faq)

### 包含使用

Hobby 计划包括每月 $5 的资源使用。

- 如果您在账单周期结束时的总资源使用费用为 $5 或更少，则不会额外收取资源使用费用。如果总资源使用费用超过 $5，则会收取超出部分的费用。
- 包含的资源使用在每个账单周期结束时重置，并不会累计。

**示例：**

- 如果您的资源使用费用为 $3，则本周期的总账单为 $5。您只需支付订阅费，因为您的资源使用低于 $5，因此已包含在订阅中。
- 如果您的资源使用费用为 $7，则本周期的总账单为 $7（$5 订阅费 + $2 使用费），因为您的资源使用超过了包含的资源使用额度。

Pro 计划不包括任何使用额度。您将按全额订阅费外加资源使用费用计费。

### 额外服务

Railway 提供“商务支持”作为 Pro 计划的附加服务。Enterprise 计划包括此服务。请联系我们以开始使用。

### 代币

代币是 Railway 提供给 Hobby 计划用户的付款方式，允许他们预付订阅和资源使用费用。

**代币作为付款方式：**

- 您必须保持足够的代币余额以覆盖使用费用 + Hobby 计划费用，否则您的项目将被暂停，您的订阅也会被取消。如果订阅被取消，您需要重新订阅。
- 如果您在账单周期内的使用超出了代币余额，您将无法部署项目，项目将被暂停。
- 如果您取消订阅，任何剩余的代币余额将被清除。

代币付款方式仅适用于 Hobby 计划用户。

**购买代币：**

您可以在账户的账单页面购买代币。

**免费试用的代币赠送：**

创建新的 Trial 账户的用户将获得一次性的 $5 免费代币。

Railway 会先使用免费代币，再使用购买的代币。试用计划用户在升级到 Hobby 计划之前无法购买代币。

有关 Railway 免费试用的更多信息，请访问 [这里](https://railway.app).

### 部分月份收费

在某些情况下，您的账单方法可能会在账单周期早期对部分账单金额收费。这确保了您的账户保持良好状态，并帮助我们减少风险和欺诈。

# 参考资料

https://docs.railway.app/reference/pricing/plans#free-trial

* any list
{:toc}