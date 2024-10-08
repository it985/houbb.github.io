---
layout: post
title: iexcel-excel 大文件读取和写入-04-order 指定列顺序
date:  2016-7-19 12:26:11 +0800
categories: [Java]
tags: [java, tool, sf]
published: true
---

# excel 系列

[Excel Export 踩坑注意点+导出方案设计](https://houbb.github.io/2016/07/19/java-tool-excel-export-design-01-overview)

[基于 hutool 的 EXCEL 优化实现](https://houbb.github.io/2016/07/19/java-tool-excel-hutool-opt-01-intro)

[iexcel-excel 大文件读取和写入，解决 excel OOM 问题-01-入门介绍](https://houbb.github.io/2016/07/19/java-tool-excel-iexcel-01-intro)

[iexcel-excel 大文件读取和写入-02-Excel 引导类简介](https://houbb.github.io/2016/07/19/java-tool-excel-iexcel-02-excelbs)

[iexcel-excel 大文件读取和写入-03-@ExcelField 注解介绍](https://houbb.github.io/2016/07/19/java-tool-excel-iexcel-03-excelField)

[iexcel-excel 大文件读取和写入-04-order 指定列顺序](https://houbb.github.io/2016/07/19/java-tool-excel-iexcel-04-order)

[iexcel-excel 大文件读取和写入-05-file bytes 获取文件字节信息](https://houbb.github.io/2016/07/19/java-tool-excel-iexcel-05-file-bytes)

[Aapche POI java excel 操作工具包入门](https://houbb.github.io/2016/07/19/java-tool-excel-poi-01-intro)

# java 字段重排序

一般实现都是基于 java 反射直接获取 Field 字段列表，一般而言这个顺序是固定的。

可是有时候，比如为了内存对齐，jvm 可能会对 Field 信息进行调整，从而导致顺序的不确定性。

这种概率很低，但是还是会发生。

可能你面对的是苛刻的产品，也可能是严格的客户，也可能是对技术的追求，这个问题一定要解决。

问题就是用来解决的。

# order 属性

## 方法定义

```java
/**
 * 指定顺序编号
 * 解释：默认是按照 Field 信息直接反射处理的，但是有一种情况反射的字段顺序可能会是错乱的。
 * 如进行内存对齐的时候，这个概率虽然很低，但是可以通过指定 order 属性来处理。
 * （1）order 值越小，生成 excel 列越靠前。
 * （2）无注解的字段默认 order=0
 * @return 顺序编号
 * @since 0.0.5
 */
int order() default 0;
```

## 解决方案

我们选择比较简单的一种方式，通过为 `@ExcelField` 指定一个 `order` 属性。

通过指定 order 属性来决定 excel 生成结果的顺序。

为了和原来兼容，默认 order=0，不指定注解时，默认 order=0。

# 使用简介

参见 `ExcelBsOrderTest` 测试类。

## 对象定义

```java
public class UserFieldOrdered {

    @ExcelField(headName = "姓名", order = 1)
    private String name;

    @ExcelField(headName = "年龄", order = 2)
    private int age;

    @ExcelField(headName = "地址", order = 0)
    private String address;

    //Getter/Setter/toString()
}
```

## 测试代码

```java
final String filePath = PathUtil.getAppTestResourcesPath()+"/userOrdered.xls";
List<UserFieldOrdered> models = buildUserList();
ExcelBs.newInstance(filePath).write(models);
```

## 生成 excel 效果

```
地址	    姓名	  年龄
china	one	  10
```

# 其他

不知道基于 ASM 字节码直接操作结果如何，后续会做相关的优化。


* any list
{:toc}