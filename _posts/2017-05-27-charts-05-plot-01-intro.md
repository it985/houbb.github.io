---
layout: post
title:  Plotly 函数图像绘制
date:  2017-5-27 13:15:25 +0800
categories: [UI]
tags: [ui, html, chart, math, js]
published: true
---

# 常见的图形库系列

[常见的图形库概览-00-overview](https://houbb.github.io/2017/05/27/charts-01-overview)

[常见的图形库概览-01-Chart.js 入门例子](https://houbb.github.io/2017/05/27/charts-02-charts-js-01-intro)

[常见的图形库概览-03-D3.js 入门例子](https://houbb.github.io/2017/05/27/charts-03-d3-js-01-intro)

[HighCharts 交互式图表-01-入门介绍](https://houbb.github.io/2017/05/27/charts-04-highchart-01-intro)

[Plotly 函数图像绘制](https://houbb.github.io/2017/05/27/charts-05-plot-01-intro)

[ApexCharts 图表入门例子](https://houbb.github.io/2017/05/27/charts-06-ApexCharts-01-intro)

[Victory 图表基于 React，适合 React 项目，支持移动端](https://houbb.github.io/2017/05/27/charts-07-victory-01-intro)

[Recharts 入门例子](https://houbb.github.io/2017/05/27/charts-08-recharts-01-intro)

[AntV G2 入门例子](https://houbb.github.io/2017/05/27/charts-09-antv-G2-01-intro)

[图表库 C3.js  入门例子](https://houbb.github.io/2017/05/27/charts-10-c3-js-01-intro)

[图表库 Google Charts  入门例子](https://houbb.github.io/2017/05/27/charts-11-google-charts-01-intro)

[ECharts-01-图表库系列](https://houbb.github.io/2017/05/27/echart-01-intro)

# Plotly

## 缘起

这两天想在前端展现数学函数图像，猜测应该有成熟的 js 库。

于是，简单的进行了尝试。

最后决定使用 [plotly.js](https://github.com/plotly/plotly.js)，其他的比如 [function-plot](https://github.com/mauriciopoppe/function-plot) 看起来也不错，以后有时间再看。

## Plotly

[plotly.js](https://plot.ly/javascript) is the open source JavaScript graphing library that powers Plotly.

Plotly 可以称之为迄今最优秀的绘图库，没有之一。

# 简单案例

## 代码

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>plot 绘制图像</title>
</head>

<body>
<div id="tester" style="width:600px;height:250px;"></div>
</body>

<script src="https://cdn.plot.ly/plotly-1.2.0.min.js"></script>


<!-- test -->
<script>
    TESTER = document.getElementById('tester');
    Plotly.plot(TESTER, [{
        x: [1, 2, 3, 4, 5],
        y: [1, 2, 4, 8, 16]
    }], {
        margin: {t: 0}
    });
</script>

</html>
```

## 效果

<div id="tester" style="width:600px;height:250px;"></div>

<script src="https://cdn.plot.ly/plotly-1.2.0.min.js"></script>

<!-- test -->
<script>
    TESTER = document.getElementById('tester');
    Plotly.plot(TESTER, [{
        x: [1, 2, 3, 4, 5],
        y: [1, 2, 4, 8, 16]
    }], {
        margin: {t: 0}
    });
</script>


# 绘制数学图像

数学图像绘图的原理。比如说 `y = 2*x+1`，实际上就是一系列 (x,y) 的点连接而成的图像。

## 代码

```html
<div id="math-function" style="width:600px;height:250px;"></div>
<script src="https://cdn.plot.ly/plotly-1.2.0.min.js"></script>

<script>
    TESTER = document.getElementById('math-function');

    var x = [], y = [];

    for(var i = -10; i < 10; i += 1) {
        x.push(i);
        y.push(2*i+1);
    }

    Plotly.plot(TESTER, [{
        x: x,
        y: y
    }], {
        margin: {t: 0}
    });
</script>
```
## 效果

<div id="math-function" style="width:600px;height:250px;"></div>

<script>
    TESTER = document.getElementById('math-function');

    var x = [], y = [];

    for(var i = -10; i < 10; i += 1) {
        x.push(i);
        y.push(2*i+1);
    }

    Plotly.plot(TESTER, [{
        x: x,
        y: y
    }], {
        margin: {t: 0}
    });
</script>


* any list
{:toc}

