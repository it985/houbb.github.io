---
layout: post
title:  PlantUML 是绘制 uml 的一个开源项目
date:  2017-01-02 00:19:56 +0800
categories: [Design]
tags: [design, uml, tool]
published: true
---

# PlantUML

[PlantUML](http://plantuml.com/) 是绘制 uml 的一个开源项目.

支持快速绘制:

- 时序图

- 用例图

- 类图

- 活动图 (here is the legacy syntax)

- 组件图

- 状态图

- 对象图

- 部署图
 
- Timing diagram
 
同时还支持以下非UML图:

- Wireframe graphical interface

- 架构图

- Specification and Description Language (SDL)

- Ditaa diagram

- 甘特图
 
- Mathematic with AsciiMath or JLaTeXMath notation

# Example

- 原始文本

```
Bob->Alice : hello
```

- 效果

```
 ┌───┐          ┌─────┐
 │Bob│          │Alice│
 └─┬─┘          └──┬──┘
   │    hello      │   
   │──────────────>│   
 ┌─┴─┐          ┌──┴──┐
 │Bob│          │Alice│
 └───┘          └─────┘
```

# Integration

## Graphviz 安装

PlantUML 是依赖于 [Graphviz](http://www.graphviz.org/) 的
 
### MAC 安装

```
$   brew install graphviz
```

## Idea

[Idea PlantUML](https://blog.csdn.net/tterminator/article/details/78177619)

## VSCode

[VSCode PlantUML](https://blog.csdn.net/qq_15437667/article/details/70163125)

- Install PlantUML 插件

1. 直接安装插件

2. 编辑内容如下：

```uml
@startuml

Bob -> Alice : Hello, how are you
Alice -> Bob : Fine, thank you, and you?

@enduml
```

使用 <kbd>Alt</kbd>+<kbd>D</kbd> 直接开启预览

![2018-04-09-plantuml-vscode.png](https://raw.githubusercontent.com/houbb/resource/master/img/tools/uml/plantuml/2018-04-09-plantuml-vscode.png)

* any list
{:toc}

