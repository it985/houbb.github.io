---
layout: post
title: 操作系统学习-05-06-一个进程最多可以创建多少个线程？
date:  2023-04-03 +0800
categories: [Linux]
tags: [linux, os, sh]
published: true
---


# 接着，来看看读者那个面经题目：一个进程最多可以创建多少个线程？

这个问题跟两个东西有关系：

1. 进程的虚拟内存空间上限，因为创建一个线程，操作系统需要为其分配一个栈空间，如果线程数量越多，所需的栈空间就要越大，那么虚拟内存就会占用的越多。

2. 系统参数限制，虽然 Linux 并没有内核参数来控制单个进程创建的最大线程个数，但是有系统级别的参数来控制整个系统的最大线程个数。

我们先看看，在进程里创建一个线程需要消耗多少虚拟内存大小？

我们可以执行 ulimit -a 这条命令，查看进程创建线程时默认分配的栈空间大小，比如我这台服务器默认分配给线程的栈空间大小为 8M。

在前面我们知道，在 32 位 Linux 系统里，一个进程的虚拟空间是 4G，内核分走了1G，留给用户用的只有 3G。

那么假设创建一个线程需要占用 10M 虚拟内存，总共有 3G 虚拟内存可以使用。于是我们可以算出，最多可以创建差不多 300 个（3G/10M）左右的线程。

如果你想自己做个实验，你可以找台 32 位的 Linux 系统运行下面这个代码：

如果想使得进程创建上千个线程，那么我们可以调整创建线程时分配的栈空间大小，比如调整为 512k：

```sh
$ ulimit -s 512
```

说完 32 位系统的情况，我们来看看 64 位系统里，一个进程能创建多少线程呢？

我的测试服务器的配置：

64 位系统；
2G 物理内存；

单核 CPU。

64 位系统意味着用户空间的虚拟内存最大值是 128T，这个数值是很大的，如果按创建一个线程需占用 10M 栈空间的情况来算，那么理论上可以创建 128T/10M 个线程，也就是 1000多万个线程，有点魔幻！

所以按 64 位系统的虚拟内存大小，理论上可以创建无数个线程。

事实上，肯定创建不了那么多线程，除了虚拟内存的限制，还有系统的限制。

比如下面这三个内核参数的大小，都会影响创建线程的上限：

/proc/sys/kernel/threads-max，表示系统支持的最大线程数，默认值是 14553；
/proc/sys/kernel/pid_max，表示系统全局的 PID 号数值的限制，每一个进程或线程都有 ID，ID 的值超过这个数，进程或线程就会创建失败，默认值是 32768；
/proc/sys/vm/max_map_count，表示限制一个进程可以拥有的VMA(虚拟内存区域)的数量，具体什么意思我也没搞清楚，反正如果它的值很小，也会导致创建线程失败，默认值是 65530。

那接下针对我的测试服务器的配置，看下一个进程最多能创建多少个线程呢？

可以看到，创建了 14374 个线程后，就无法在创建了，而且报错是因为资源的限制。

前面我提到的 `threads-max` 内核参数，它是限制系统里最大线程数，默认值是 14553。

我们可以运行那个测试线程数的程序后，看下当前系统的线程数是多少，可以通过 top -H 查看。

左上角的 Threads 的数量显示是 14553，与 threads-max 内核参数的值相同，所以我们可以认为是因为这个参数导致无法继续创建线程。

那么，我们可以把 threads-max 参数设置成 99999:

```sh
echo 99999 > /proc/sys/kernel/threads-max
```

可以看到，当进程创建了 32326 个线程后，就无法继续创建里，且报错是无法继续申请内存。

此时的上限个数很接近 pid_max 内核参数的默认值（32768），那么我们可以尝试将这个参数设置为 99999：

```sh
echo 99999 > /proc/sys/kernel/pid_max
```

设置完 pid_max 参数后，继续跑测试线程数的程序，运行后结果创建线程的个数还是一样卡在了 32768 了。

当时我也挺疑惑的，明明 pid_max 已经调整大后，为什么线程个数还是上不去呢？

后面经过查阅资料发现，max_map_count 这个内核参数也是需要调大的，但是它的数值与最大线程数之间有什么关系，我也不太明白，只是知道它的值是会限制创建线程个数的上限。

然后，我把 max_map_count 内核参数也设置成后 99999：

```sh
echo 99999 > /proc/sys/kernel/max_map_count 
```

当创建差不多 5 万个线程后，我的服务器就卡住不动了，CPU 都已经被占满了，毕竟这个是单核 CPU，所以现在是 CPU 的瓶颈了。

我只有这台服务器，如果你们有性能更强的服务器来测试的话，有兴趣的小伙伴可以去测试下。

接下来，我们换个思路测试下，把创建线程时分配的栈空间调大，比如调大为 100M，在大就会创建线程失败。

# 小结

简单总结下：

32 位系统，用户态的虚拟空间只有 3G，如果创建线程时分配的栈空间是 10M，那么一个进程最多只能创建 300 个左右的线程。

64 位系统，用户态的虚拟空间大到有 128T，理论上不会受虚拟内存大小的限制，而会受系统的参数或性能限制。

# 参考资料

https://xiaolincoding.com/os/4_process/create_thread_max.html

* any list
{:toc}