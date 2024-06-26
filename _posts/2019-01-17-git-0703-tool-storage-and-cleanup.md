---
layout: post
title: Git 工具之储藏与清理-7.3
date: 2019-1-17 09:34:35 +0800
categories: [Git]
tags: [git, devops, git-learn, git-topic, sh]
published: true
excerpt: Git 工具之储藏与清理-7.3
---

# 储藏与清理

有时，当你在项目的一部分上已经工作一段时间后，所有东西都进入了混乱的状态，而这时你想要切换到另一个分支做一点别的事情。 

问题是，你不想仅仅因为过会儿回到这一点而为做了一半的工作创建一次提交。 

针对这个问题的答案是 `git stash` 命令。

储藏会处理工作目录的脏的状态 - 即，修改的跟踪文件与暂存改动 - 然后将未完成的修改保存到一个栈上，而你可以在任何时候重新应用这些改动。

# 储藏工作

为了演示，进入项目并改动几个文件，然后可能暂存其中的一个改动。 

如果运行 git status，可以看到有改动的状态：

```
$ git status
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   index.html

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   lib/simplegit.rb
```

现在想要切换分支，但是还不想要提交之前的工作；所以储藏修改。

将新的储藏推送到栈上，运行 git stash 或 git stash save：

```
$ git stash
Saved working directory and index state \
  "WIP on master: 049d078 added the index file"
HEAD is now at 049d078 added the index file
(To restore them type "git stash apply")
```

工作目录是干净的了：

```
$ git status
# On branch master
nothing to commit, working directory clean
```

在这时，你能够轻易地切换分支并在其他地方工作；你的修改被存储在栈上。 

要查看储藏的东西，可以使用 git stash list：

```
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert "added file_size"
stash@{2}: WIP on master: 21d80a5 added number to log
```

在本例中，有两个之前做的储藏，所以你接触到了三个不同的储藏工作。 

可以通过原来 stash 命令的帮助提示中的命令将你刚刚储藏的工作重新应用：git stash apply。 

如果想要应用其中一个更旧的储藏，可以通过名字指定它，像这样：`git stash apply stash@{2}`。 

如果不指定一个储藏，Git 认为指定的是最近的储藏：

```
$ git stash apply
# On branch master
# Changed but not updated:
#   (use "git add <file>..." to update what will be committed)
#
#      modified:   index.html
#      modified:   lib/simplegit.rb
#
```

可以看到 Git 重新修改了当你保存储藏时撤消的文件。 在本例中，当尝试应用储藏时有一个干净的工作目录，并且尝试将它应用在保存它时所在的分支；但是有一个干净的工作目录与应用在同一分支并不是成功应用储藏的充分必要条件。 可以在一个分支上保存一个储藏，切换到另一个分支，然后尝试重新应用这些修改。 当应用储藏时工作目录中也可以有修改与未提交的文件 - 如果有任何东西不能干净地应用，Git 会产生合并冲突。

文件的改动被重新应用了，但是之前暂存的文件却没有重新暂存。 想要那样的话，必须使用 --index 选项来运行 git stash apply 命令，来尝试重新应用暂存的修改。 如果已经那样做了，那么你将回到原来的位置：

```
$ git stash apply --index
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#      modified:   index.html
#
# Changed but not updated:
#   (use "git add <file>..." to update what will be committed)
#
#      modified:   lib/simplegit.rb
#
```

应用选项只会尝试应用暂存的工作 - 在堆栈上还有它。 

可以运行 `git stash drop` 加上将要移除的储藏的名字来移除它：

```
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert "added file_size"
stash@{2}: WIP on master: 21d80a5 added number to log
$ git stash drop stash@{0}
Dropped stash@{0} (364e91f3f268f0900bc3ee613f9f733e82aaed43)
```

也可以运行 git stash pop 来应用储藏然后立即从栈上扔掉它。

# 创造性的储藏

有几个储藏的变种可能也很有用。 第一个非常流行的选项是 stash save 命令的 --keep-index 选项。 

它告诉 Git 不要储藏任何你通过 git add 命令已暂存的东西。

当你做了几个改动并只想提交其中的一部分，过一会儿再回来处理剩余改动时，这个功能会很有用。

```
$ git status -s
M  index.html
 M lib/simplegit.rb

$ git stash --keep-index
Saved working directory and index state WIP on master: 1b65b17 added the index file
HEAD is now at 1b65b17 added the index file

$ git status -s
M  index.html
```

另一个经常使用储藏来做的事情是像储藏跟踪文件一样储藏未跟踪文件。 默认情况下，git stash 只会储藏已经在索引中的文件。 

如果指定 --include-untracked 或 -u 标记，Git 也会储藏任何创建的未跟踪文件。

```
$ git status -s
M  index.html
 M lib/simplegit.rb
?? new-file.txt

$ git stash -u
Saved working directory and index state WIP on master: 1b65b17 added the index file
HEAD is now at 1b65b17 added the index file

$ git status -s
$
```

最终，如果指定了 --patch 标记，Git 不会储藏所有修改过的任何东西，但是会交互式地提示哪些改动想要储藏、哪些改动需要保存在工作目录中。

```
$ git stash --patch
diff --git a/lib/simplegit.rb b/lib/simplegit.rb
index 66d332e..8bb5674 100644
--- a/lib/simplegit.rb
+++ b/lib/simplegit.rb
@@ -16,6 +16,10 @@ class SimpleGit
         return `#{git_cmd} 2>&1`.chomp
       end
     end
+
+    def show(treeish = 'master')
+      command("git show #{treeish}")
+    end

 end
 test
Stash this hunk [y,n,q,a,d,/,e,?]? y

Saved working directory and index state WIP on master: 1b65b17 added the index file
```

# 从储藏创建一个分支

如果储藏了一些工作，将它留在那儿了一会儿，然后继续在储藏的分支上工作，在重新应用工作时可能会有问题。 如果应用尝试修改刚刚修改的文件，你会得到一个合并冲突并不得不解决它。 

如果想要一个轻松的方式来再次测试储藏的改动，可以运行 git stash branch 创建一个新分支，检出储藏工作时所在的提交，重新在那应用工作，然后在应用成功后扔掉储藏：

```
$ git stash branch testchanges
Switched to a new branch "testchanges"
# On branch testchanges
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#      modified:   index.html
#
# Changed but not updated:
#   (use "git add <file>..." to update what will be committed)
#
#      modified:   lib/simplegit.rb
#
Dropped refs/stash@{0} (f0dfc4d5dc332d1cee34a634182e168c4efc3359)
```

这是在新分支轻松恢复储藏工作并继续工作的一个很不错的途径。

# 清理工作目录

对于工作目录中一些工作或文件，你想做的也许不是储藏而是移除。 

`git clean` 命令会帮你做这些事。

有一些通用的原因比如说为了移除由合并或外部工具生成的东西，或是为了运行一个干净的构建而移除之前构建的残留。

你需要谨慎地使用这个命令，因为它被设计为从工作目录中移除未被追踪的文件。 如果你改变主意了，你也不一定能找回来那些文件的内容。 一个更安全的选项是运行 git stash --all 来移除每一样东西并存放在栈中。

你可以使用`git clean`命令去除冗余文件或者清理工作目录。 使用`git clean -f -d`命令来移除工作目录中所有未追踪的文件以及空的子目录。 -f 意味着 强制 或 “确定移除”。

如果只是想要看看它会做什么，可以使用 -n 选项来运行命令，这意味着 “做一次演习然后告诉你 将要 移除什么”。

```
$ git clean -d -n
Would remove test.o
Would remove tmp/
```

默认情况下，git clean 命令只会移除没有忽略的未跟踪文件。 任何与 .gitiignore 或其他忽略文件中的模式匹配的文件都不会被移除。 

如果你也想要移除那些文件，例如为了做一次完全干净的构建而移除所有由构建生成的 .o 文件，可以给 clean 命令增加一个 -x 选项。

```
$ git status -s
 M lib/simplegit.rb
?? build.TMP
?? tmp/

$ git clean -n -d
Would remove build.TMP
Would remove tmp/

$ git clean -n -d -x
Would remove build.TMP
Would remove test.o
Would remove tmp/
```

如果不知道 git clean 命令将会做什么，在将 -n 改为 -f 来真正做之前总是先用 -n 来运行它做双重检查。 另一个小心处理过程的方式是使用 -i 或 “interactive” 标记来运行它。

这将会以交互模式运行 clean 命令。

```
$ git clean -x -i
Would remove the following items:
  build.TMP  test.o
*** Commands ***
    1: clean                2: filter by pattern    3: select by numbers    4: ask each             5: quit
    6: help
What now>
```

这种方式下可以分别地检查每一个文件或者交互地指定删除的模式。

# 参考资料

https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%82%A8%E8%97%8F%E4%B8%8E%E6%B8%85%E7%90%86

* any list
{:toc}

