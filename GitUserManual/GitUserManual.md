
心血来潮，翻译`Git User Manual`。只翻译重点语句、我认为有用的语句。

[TOC]

# Part I. 仓库和分支 #
## 章节1  如何获得一个Git仓库 ##
最好的方式是使用`git-clone`命令下载一个已经存在的仓库，如果没有可以使用下面的例子：
```
	# Git itself (approx. 40MB download):
$ git clone git://git.kernel.org/pub/scm/git/git.git
	# the Linux kernel (approx. 640MB download):
$ git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
```

## 章节2  如何从工程检出一个不同的版本 ##
Git被认为是最好的存储文件集合的历史的工具。它把历史存储为工程内容相互关联的快照的压缩集合。在Git中每一个版本称为**commit**。这些快照并不是必须得按照从旧到新的顺序排成一列，相反地，可能是多个并行的开发同时进行，称之为**分支**，分支可以合并也可以分叉。

一个单个的Git仓库能够跟踪开发过程中的多个分支。它是通过保存一列指向每个分支最新的提交的**heads**来实现的。`git branch`命令显示一列分支头：
```
$ git branch
* master
```

大多数的工程也使用了**tags**.标签就像头一样，也是指向工程的历史，通过命令`git tag`列出所有的标签：
```
$ git tag -l
v2.6.11
v2.6.11-tree
v2.6.12
v2.6.12-rc2
v2.6.12-rc3
v2.6.12-rc4
v2.6.12-rc5
v2.6.12-rc6
v2.6.13
...
```

标签是预期用来一直指向工程中的同一个版本，而头指针是预期随着开发进程向前推进的。

创建一个分支头指向某个版本然后检出这个分支，可以使用命令`git-checkout`:
```
$ git checkout -b new v2.6.13
```

工作目录现在更新为标签v2.6.13时的内容，而且`git-branch`显示两个分支，星号指示当前检出的分支：
```
$ git branch
  master
* new
```

如果你决定更希望看到版本v2.6.17，那么使用下面的命令让当前分支指向v2.6.17:
```
$ git reset --hard v2.6.17
```

**注意**：如果当前分支头指针是你惟一一个执行历史中特定的点，那么重置分支可能会让你没有办法找到它曾经指向的历史，所以要谨慎地使用这条命令。

