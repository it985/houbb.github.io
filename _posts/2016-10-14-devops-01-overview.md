---
layout: post
title: Devops-01-devops 是什么？
date:  2016-10-14 10:15:54 +0800
categories: [Devops]
tags: [devops, ci, sh]
published: true
---

# 拓展阅读

[Devops-01-devops 是什么？](https://houbb.github.io/2016/10/14/devops-01-overview)

[Devops-02-Jpom 简而轻的低侵入式在线构建、自动部署、日常运维、项目监控软件](https://houbb.github.io/2016/10/14/devops-02-jpom)

[代码质量管理 SonarQube-01-入门介绍](https://houbb.github.io/2016/10/14/devops-sonarqube-01-intro)

[项目管理平台-01-jira 入门介绍 缺陷跟踪管理系统，为针对缺陷管理、任务追踪和项目管理的商业性应用软件](https://houbb.github.io/2016/10/14/project-manage-jira-01-intro)

[项目管理平台-01-Phabricator 入门介绍 一套集成的强大工具，帮助公司构建更高质量的软件](https://houbb.github.io/2016/10/14/project-manage-phabricator-01-overview)

[持续集成平台 01 jenkins 入门介绍](https://houbb.github.io/2016/10/14/devops-jenkins-01-intro)

[持续集成平台 02 jenkins plugin 插件](https://houbb.github.io/2016/10/14/devops-jenkins-02-plugin)

# DevOps

![flow](https://i-blog.csdnimg.cn/blog_migrate/7c1db52deec6e03c46f2f2ce3c63d1e4.png)

1、PLAN   开发团队根据客户的目标指定开发计划
 
2、CODE    根据"PLAN(开发计划)" 开始编写代码，需要将不同版本("稳定"/"最新")的代码存储在一个库中
 
3、Build   代码编写完成后，需要将代码构建打包并且运行
 
4、Test    成功构建项目后，需要测试代码是否存在BUG或者错误
 
5、DEPLOY   代码经过"手动调试"和"自动化测试"后，认为可以部署了，选一个稳定版本部署
 
6、OPERATE   运维团队将代码部署到生产环境中
 
7、MONITOR   项目部署上线后，需要持续的监控产品
 
8、INTEGRATE   然后将监控阶段收到的反馈发送回PLAN阶段，整体反复的流程就是DEVOPS的核心(ci/cd)

# 如何实现 devops 流程

1、开发人员将编写好的代码上传到gitlab代码仓库
 
2、我们通过手动/自动的形式通过Jenkins将代码拉取下来
 
3、jenkins会通过maven工具开始对代码构建(nexus 等包仓库)
 
4、如果编译没问题，jenkins会将打好的jar包封装成镜像发给harbor镜像仓库
 
5、jenkins再去通知服务器端通过docker/k8s拉取镜像并运行服务

# 技术选型

在初期，最主要的是让整个流程跑起来。

除了购买已有的服务，可以优先选择开源：

主流工具就是业内大家用得比较多的，在各种分享文章里面高频出现的，使用经验一搜一大把的那种工具。

我给你提供一些工具，你可以参考一下：

```
需求管理工具Jira；
知识管理工具Confluence；

版本控制系统GitLab；
持续集成工具Jenkins；
代码质量工具SonarQube；
构建工具Maven/Gradle；
制品管理Artifactory/Harbor；
配置管理工具Ansible；
配置中心Apollo；
测试工具RF/Selenium/Appium/Jmeter/TestNG；
安全合规工具BlackDuck/Fortify；
环境管理-虚拟化：k8s docker 
```

====> 企业级**度量平台**

# 参考资料

https://blog.csdn.net/qq_42883074/article/details/125842147

## 其他开源

[蓝鲸持续集成平台(蓝盾)](https://github.com/TencentBlueKing/bk-ci)

* any list
{:toc}



