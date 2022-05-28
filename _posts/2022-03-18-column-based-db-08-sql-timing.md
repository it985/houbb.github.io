---
layout: post
title: 列式数据库-08-SQL Timing
date: 2022-03-18 21:01:55 +0800 
categories: [Database]
tags: [database, monetdb, column-based-db, sh]
published: true
---

# SQL Timing

以多种方式支持查询执行的计时，但很大程度上取决于您要测量的内容。点对点挂钟时间，或内核操作的实际行为。

基线是使用简单的命令行工具，例如在 Linux 上评估针对 mclient 运行脚本的性能。注意 /bin/time 和 /usr/bin/time 不一样，它们主要测量和报告给定命令/进程所花费的挂钟时间。有关详细信息，请参见各自的手册页。

下一种方法是使用 mclient 工具的 --interactive 选项，该选项将在脚本中以简单易懂的方式报告每个 SQL 查询的时间。它返回将查询发送到服务器和接收第一个答案块之间的挂钟时间。

查询历史记录也可以保存在单独的日志中以进行后期分析。 （见下文）

因此，(1) 包括从加载 mclient 二进制文件和启动 mclient 进程、在 mclient 中解析查询、发送到服务器、让服务器执行查询并序列化结果、将结果发送回客户端、到客户端接收、解析和呈现结果，并将结果发送到 /dev/null（“免费”）、文件（I/O）或终端（滚动）。 (2) 仅包括服务器接收和执行查询以及创建结果所花费的时间。客户端接收、解析、渲染等的上述成本不包括在内。 (3) 也一样。

通过在查询前加上修饰符 TRACE 可以获得 SQL 查询的详细时间。它将生成一个可查询的表，其中包含所有关系代数操作的分解（参见 TRACE 命令）。分析工具听诊器为那些对系统内部工作感兴趣的人提供了更多详细信息。它提供了许多系统参数的挂钩，例如输入/输出、CPU 周期和线程的活动。

应该小心地为数据库查询计时。通常，您会注意到多次运行相同查询的响应时间差异。根本原因可能是数据本身驻留在磁盘上（慢）或已在内存缓存中可用（快），单个用户运行查询（快）或必须与其他用户竞争（慢），包括与其他用户竞争机器上的进程争夺 CPU、内存和 IO 资源。作为预防措施，您可能需要刷新系统缓存。 Windows 工具会刷新缓存。您需要按下“Flush Cache WS”和“Flush All Standby”按钮。在 Linux 上，您必须创建一个消耗所有内存的小作业。


# 参考资料

https://www.monetdb.org/documentation-Jan2022/user-guide/query-timing/

* any list
{:toc}