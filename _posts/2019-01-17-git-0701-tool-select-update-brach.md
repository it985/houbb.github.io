---
layout: post
title: Git 工具之选择修改分支-7.1
date: 2019-1-17 09:34:35 +0800
categories: [Git]
tags: [git, devops, git-learn, git-topic, sh]
published: true
excerpt: Git 工具之选择修改分支-7.1
---

# 7.1 Git 工具 - 选择修订版本

现在，你已经学习了管理或者维护 Git 仓库、实现代码控制所需的大多数日常命令和工作流程。 

你已经尝试了跟踪和提交文件的基本操作，并且发挥了暂存区和轻量级的分支及合并的威力。

接下来你将学习一些 Git 的强大功能，这些功能你可能并不会在日常操作中使用，但在某些时候你可能会需要。

# 选择修订版本

Git 允许你通过几种方法来指明特定的或者一定范围内的提交。 了解它们并不是必需的，但是了解一下总没坏处。

## 单个修订版本

你可以通过 Git 给出的 SHA-1 值来获取一次提交，不过还有很多更人性化的方式来做同样的事情。 本节将会介绍获取单个提交的多种方法。

## 简短的 SHA-1

Git 十分智能，你只需要提供 SHA-1 的前几个字符就可以获得对应的那次提交，当然你提供的 SHA-1 字符数量不得少于 4 个，并且没有歧义——也就是说，当前仓库中只有一个对象以这段 SHA-1 开头。

例如查看一次指定的提交，假设你执行 git log 命令来查看之前新增一个功能的那次提交:

```
$ git log
commit 734713bc047d87bf7eac9674765ae793478c50d3
Author: Scott Chacon <schacon@gmail.com>
Date:   Fri Jan 2 18:32:33 2009 -0800

    fixed refs handling, added gc auto, updated tests

commit d921970aadf03b3cf0e71becdaab3147ba71cdef
Merge: 1c002dd... 35cfb2b...
Author: Scott Chacon <schacon@gmail.com>
Date:   Thu Dec 11 15:08:43 2008 -0800

    Merge commit 'phedders/rdocs'

commit 1c002dd4b536e7479fe34593e72e6c6c1819e53b
Author: Scott Chacon <schacon@gmail.com>
Date:   Thu Dec 11 14:58:32 2008 -0800

    added some blame and merge stuff
```

假设这个提交是 1c002dd....，如果你想 git show 这个提交，下面的命令是等价的（假设简短的版本没有歧义）：

```
$ git show 1c002dd4b536e7479fe34593e72e6c6c1819e53b
$ git show 1c002dd4b536e7479f
$ git show 1c002d
```

Git 可以为 SHA-1 值生成出简短且唯一的缩写。 

如果你在 git log 后加上 --abbrev-commit 参数，输出结果里就会显示简短且唯一的值；默认使用七个字符，不过有时为了避免 SHA-1 的歧义，会增加字符数：

```
$ git log --abbrev-commit --pretty=oneline
ca82a6d changed the version number
085bb3b removed unnecessary test code
a11bef0 first commit
```

通常 8 到 10 个字符就已经足够在一个项目中避免 SHA-1 的歧义。

比如 Linux 内核这个相当大的 Git 项目，目前有超过 45 万个提交，包含 360 万个对象，也只需要前 11 个字符就能保证唯一性。

## 关于 SHA-1 的简短说明

许多人觉得他们的仓库里有可能出现两个 SHA-1 值相同的对象。 

如果你真的向仓库里提交了一个跟之前的某个对象具有相同 SHA-1 值的对象，Git 发现仓库里已经存在了拥有相同 HASH 值的对象，就会认为这个新的提交是已经被写入仓库的。 如果之后你想检出那个对象时，你将得到先前那个对象的数据。

但是这种情况发生的概率十分渺小。 SHA-1 摘要长度是 20 字节，也就是 160 位。 2^80 个随机哈希对象才有 50% 的概率出现一次冲突 

（计算冲突机率的公式是 `p = (n(n-1)/2) * (1/2^160))` ）。

2^80 是 1.2 x 10^24 也就是一亿亿亿。 那是地球上沙粒总数的 1200 倍。

举例说一下怎样才能产生一次 SHA-1 冲突。 如果地球上 65 亿个人类都在编程，每人每秒都在产生等价于整个 Linux 内核历史（360 万个 Git 对象）的代码，并将之提交到一个巨大的 Git 仓库里面，这样持续两年的时间才会产生足够的对象，使其拥有 50% 的概率产生一次 SHA-1 对象冲突。 这要比你编程团队的成员同一个晚上在互不相干的意外中被狼袭击并杀死的机率还要小。

# 分支引用

指明一次提交最直接的方法是有一个指向它的分支引用。 这样你就可以在任意一个 Git 命令中使用这个分支名来代替对应的提交对象或者 SHA-1 值。 

例如，你想要查看一个分支的最后一次提交的对象，假设 topic1 分支指向 ca82a6d ，那么以下的命令是等价的:

```
$ git show ca82a6dff817ec66f44342007202690a93763949
$ git show topic1
```

如果你想知道某个分支指向哪个特定的 SHA-1，或者想看任何一个例子中被简写的 SHA-1 ，你可以使用一个叫做 rev-parse 的 Git 探测工具。 你可以在 Git 内部原理 中查看更多关于探测工具的信息。简单来说，rev-parse 是为了底层操作而不是日常操作设计的。 不过，有时你想看 Git 现在到底处于什么状态时，它可能会很有用。 你可以在你的分支上执行 rev-parse

```
$ git rev-parse topic1
ca82a6dff817ec66f44342007202690a93763949
```

# 引用日志

当你在工作时， Git 会在后台保存一个引用日志(reflog)，引用日志记录了最近几个月你的 HEAD 和分支引用所指向的历史。

你可以使用 git reflog 来查看引用日志

```
$ git reflog
734713b HEAD@{0}: commit: fixed refs handling, added gc auto, updated
d921970 HEAD@{1}: merge phedders/rdocs: Merge made by recursive.
1c002dd HEAD@{2}: commit: added some blame and merge stuff
1c36188 HEAD@{3}: rebase -i (squash): updating HEAD
95df984 HEAD@{4}: commit: # This is a combination of two commits.
1c36188 HEAD@{5}: rebase -i (squash): updating HEAD
7e05da5 HEAD@{6}: rebase -i (pick): updating HEAD
```

每当你的 HEAD 所指向的位置发生了变化，Git 就会将这个信息存储到引用日志这个历史记录里。 通过这些数据，你可以很方便地获取之前的提交历史。 如果你想查看仓库中 HEAD 在五次前的所指向的提交，你可以使用 @{n} 来引用 reflog 中输出的提交记录。

`$ git show HEAD@{5}`

你同样可以使用这个语法来查看某个分支在一定时间前的位置。 例如，查看你的 master 分支在昨天的时候指向了哪个提交，你可以输入

`$ git show master@{yesterday}`

就会显示昨天该分支的顶端指向了哪个提交。 这个方法只对还在你引用日志里的数据有用，所以不能用来查好几个月之前的提交。

可以运行 git log -g 来查看类似于 git log 输出格式的引用日志信息：

```
$ git log -g master
commit 734713bc047d87bf7eac9674765ae793478c50d3
Reflog: master@{0} (Scott Chacon <schacon@gmail.com>)
Reflog message: commit: fixed refs handling, added gc auto, updated
Author: Scott Chacon <schacon@gmail.com>
Date:   Fri Jan 2 18:32:33 2009 -0800

    fixed refs handling, added gc auto, updated tests

commit d921970aadf03b3cf0e71becdaab3147ba71cdef
Reflog: master@{1} (Scott Chacon <schacon@gmail.com>)
Reflog message: merge phedders/rdocs: Merge made by recursive.
Author: Scott Chacon <schacon@gmail.com>
Date:   Thu Dec 11 15:08:43 2008 -0800

    Merge commit 'phedders/rdocs'
```

值得注意的是，引用日志只存在于本地仓库，一个记录你在你自己的仓库里做过什么的日志。 其他人拷贝的仓库里的引用日志不会和你的相同；而你新克隆一个仓库的时候，引用日志是空的，因为你在仓库里还没有操作。 `git show HEAD@{2.months.ago}` 这条命令只有在你克隆了一个项目至少两个月时才会有用——如果你是五分钟前克隆的仓库，那么它将不会有结果返回。

## 将引用日志想作 Git 版的 shell 历史记录

如果你有 UNIX 或者 Linux 的背景，你不妨将引用日志想作 Git 版的 shell 历史记录，重点在于仅与你和你的会话相关，而与他人无关。

# 祖先引用

祖先引用是另一种指明一个提交的方式。 

如果你在引用的尾部加上一个 `^`， Git 会将其解析为该引用的上一个提交。 假设你的提交历史是：

```
$ git log --pretty=format:'%h %s' --graph
* 734713b fixed refs handling, added gc auto, updated tests
*   d921970 Merge commit 'phedders/rdocs'
|\
| * 35cfb2b Some rdoc changes
* | 1c002dd added some blame and merge stuff
|/
* 1c36188 ignore *.gem
* 9b29157 add open3_detach to gemspec file list
```

你可以使用 HEAD^ 来查看上一个提交，也就是 “HEAD 的父提交”：

```
$ git show HEAD^
commit d921970aadf03b3cf0e71becdaab3147ba71cdef
Merge: 1c002dd... 35cfb2b...
Author: Scott Chacon <schacon@gmail.com>
Date:   Thu Dec 11 15:08:43 2008 -0800

    Merge commit 'phedders/rdocs'
```

你也可以在 `^` 后面添加一个数字——例如 d921970^2 代表 “d921970 的第二父提交” 这个语法只适用于合并(merge)的提交，因为合并提交会有多个父提交。 第一父提交是你合并时所在分支，而第二父提交是你所合并的分支：

```
$ git show d921970^
commit 1c002dd4b536e7479fe34593e72e6c6c1819e53b
Author: Scott Chacon <schacon@gmail.com>
Date:   Thu Dec 11 14:58:32 2008 -0800

    added some blame and merge stuff

$ git show d921970^2
commit 35cfb2b795a55793d7cc56a6cc2060b4bb732548
Author: Paul Hedderly <paul+git@mjr.org>
Date:   Wed Dec 10 22:22:03 2008 +0000

    Some rdoc changes
```

另一种指明祖先提交的方法是 `~`。 

同样是指向第一父提交，因此 HEAD~ 和 HEAD^ 是等价的。 而区别在于你在后面加数字的时候。 

HEAD~2 代表 “第一父提交的第一父提交”，也就是 “祖父提交” —— Git 会根据你指定的次数获取对应的第一父提交。 

例如，在之前的列出的提交历史中，HEAD~3 就是

```
$ git show HEAD~3
commit 1c3618887afb5fbcbea25b7c013f4e2114448b8d
Author: Tom Preston-Werner <tom@mojombo.com>
Date:   Fri Nov 7 13:47:59 2008 -0500

    ignore *.gem
```

也可以写成 HEAD^^^，也是第一父提交的第一父提交的第一父提交：

```
$ git show HEAD^^^
commit 1c3618887afb5fbcbea25b7c013f4e2114448b8d
Author: Tom Preston-Werner <tom@mojombo.com>
Date:   Fri Nov 7 13:47:59 2008 -0500

    ignore *.gem
```

你也可以组合使用这两个语法 —— 你可以通过 HEAD~3^2 来取得之前引用的第二父提交（假设它是一个合并提交）。


# 提交区间

你已经学会如何单次的提交，现在来看看如何指明一定区间的提交。 

当你有很多分支时，这对管理你的分支时十分有用，你可以用提交区间来解决 “这个分支还有哪些提交尚未合并到主分支？” 的问题

## 双点

最常用的指明提交区间语法是双点。 这种语法可以让 Git 选出在一个分支中而不在另一个分支中的提交。 

例如，你有如下的提交历史 Example history for range selection.

![Figure 137. Example history for range selection.](https://git-scm.com/book/en/v2/images/double-dot.png)

你想要查看 experiment 分支中还有哪些提交尚未被合并入 master 分支。 

你可以使用 master..experiment 来让 Git 显示这些提交。也就是 “在 experiment 分支中而不在 master 分支中的提交”。 为了使例子简单明了，我使用了示意图中提交对象的字母来代替真实日志的输出，所以会显示：

```
$ git log master..experiment
D
C
```

反过来，如果你想查看在 master 分支中而不在 experiment 分支中的提交，你只要交换分支名即可。 experiment..master 会显示在 master 分支中而不在 experiment 分支中的提交：

```
$ git log experiment..master
F
E
```

这可以让你保持 experiment 分支跟随最新的进度以及查看你即将合并的内容。 

另一个常用的场景是查看你即将推送到远端的内容：

```
$ git log origin/master..HEAD
```

这个命令会输出在你当前分支中而不在远程 origin 中的提交。 

如果你执行了 git push 并且你的当前分支正在跟踪 origin/master，git log origin/master..HEAD 所输出的提交将会被传输到远端服务器。 

如果你留空了其中的一边， Git 会默认为 HEAD。 

例如， git log origin/master.. 将会输出与之前例子相同的结果 —— Git 使用 HEAD 来代替留空的一边。

## 多点

双点语法很好用，但有时候你可能需要两个以上的分支才能确定你所需要的修订，比如查看哪些提交是被包含在某些分支中的一个，但是不在你当前的分支上。 Git 允许你在任意引用前加上 `^` 字符或者 `--not` 来指明你不希望提交被包含其中的分支。 

因此下列3个命令是等价的：

```
$ git log refA..refB
$ git log ^refA refB
$ git log refB --not refA
```

这个语法很好用，因为你可以在查询中指定超过两个的引用，这是双点语法无法实现的。 

比如，你想查看所有被 refA 或 refB 包含的但是不被 refC 包含的提交，你可以输入下面中的任意一个命令

```
$ git log refA refB ^refC
$ git log refA refB --not refC
```

这就构成了一个十分强大的修订查询系统，你可以通过它来查看你的分支里包含了哪些东西。

## 三点

最后一种主要的区间选择语法是三点，这个语法可以选择出被两个引用中的一个包含但又不被两者同时包含的提交。 

再看看之前双点例子中的提交历史。 如果你想看 master 或者 experiment 中包含的但不是两者共有的提交，你可以执行

```
$ git log master...experiment
F
E
D
C
```

这和通常 log 按日期排序的输出一样，仅仅给出了4个提交的信息。

这种情形下，log 命令的一个常用参数是 --left-right，它会显示每个提交到底处于哪一侧的分支。 这会让输出数据更加清晰。

```
$ git log --left-right master...experiment
< F
< E
> D
> C
```

有了这些工具，你就可以十分方便地查看你 Git 仓库中的提交。

# 参考资料

https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%80%89%E6%8B%A9%E4%BF%AE%E8%AE%A2%E7%89%88%E6%9C%AC

* any list
{:toc}

