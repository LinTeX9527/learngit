
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


## 章节3  理解历史：提交 ##
工程历史的每次的变化都由一个提交来表示。命令`git-show`显示当前分支最近的一次提交：
```
$ git show
commit 17cf781661e6d38f737f15f53ab552f1e95960d7
Author: Linus Torvalds <torvalds@ppc970.osdl.org.(none)>
Date:   Tue Apr 19 14:11:06 2005 -0700

    Remove duplicate getenv(DB_ENVIRONMENT) call

    Noted by Tony Luck.

diff --git a/init-db.c b/init-db.c
index 65898fa..b002dc6 100644
--- a/init-db.c
+++ b/init-db.c
@@ -7,7 +7,7 @@

 int main(int argc, char **argv)
 {
-	char *sha1_dir = getenv(DB_ENVIRONMENT), *path;
+	char *sha1_dir, *path;
 	int len, i;

 	if (mkdir(".git", 0755) < 0) {
```

一个提交可以显示是谁做了最近的修改，它们做了什么，原因是什么。

每一个提交都有一个40位的十六进制的ID，又称为**对象名字**或者**SHA-1 ID**，正如上面的`git show`命令输出的第一行所示。引用一个提交可以使用简短的名字，例如一个标签或者分支名字，但是这个更长的名字也有用。最为重要的是，对于这个提交它是全球唯一的名字，因此假如你告诉某个人对象的名字（例如在邮件中），你可以保证这个名字指向他们仓库中同样的提交，正如它指向你仓库中同样的提交（假如他们仓库中也有这个提交）。既然对象名字是把提交内容经过hash算出来的，你可以保证提交不变它的名字也不变。

### 理解历史：提交，父辈，可存取性 ###
每一个提交（除了工程中的第一个提交）都有一个父辈提交，父辈提交显示了在这个提交之前发生了什么。跟着父辈链条往前最终会追溯到这个工程的开始。然而Git允许多个开发同时进行，开发中两个分支的重新汇合称为**merge**，代表merge的这个提交有两个父辈，每一个父辈代表那个开发中最近的提交。

在下面，我们这样称呼：如果提交X是提交Y的父辈，就说提交X是提交Y可以存取的。同样的，也可以说提交Y是提交X的后代。

### 理解历史：历史图表 ###
我们可以使用图表来表示Git提交历史。每一个提交用**o**表示，时间从左到右。

```
         o--o--o <-- Branch A
        /
 o--o--o <-- master
        \
         o--o--o <-- Branch B
```

### 理解历史：分支是什么 ###
为了精准性，我们使用**分支**来表示一条开发线，使用**分支头**（又或者**头指针**）来表示那个分支最近的一个提交。在上面的例子中，分支头指针A指向一个特定的提交，但当我们引用这个提交一条线上的3个提交把它们看做是分支A的一部分。

不管怎样，只要不产生混淆，我们通常使用**分支**指代分支和分支头。



## 章节4  操作分支 ##

创建、删除、修改分支都非常的快而且简单，下面是命令总结：
	- `git branch` 列出所有分支
	- `git branch <branch>` 创建一个新的分支并命名为**branch**，指向历史中当前分支指向的同样的点
	- `git branch <branch> <start-point>` 创建一个新的分支并命名为**branch**，指向**start-point**，它可以任由你指定，可以是分支名也可以是标签名。
	- `git branch -d <branch>` 删除分支**branch**，如果分支没有充分地合并到上游分支或者被当前分支包含，这个命令会出错并且显示警告信息。
	- `git branch -D <branch>` 删除分支**branch**不管它的合并状态如何。
	- `git checkout <branch>` 切换当前分支为**branch**，从分支**branch**指向的历史来更新工作目录。
	- `git checkout -b <new> <start-point>` 创建一个分支**new**并且指向**start-point**，然后检出分支**new**。

特殊符号**HEAD**一直都指向当前分支。事实上Git使用**.git**目录下的一个名字为**HEAD**的文件来记住当前分支是哪一个。

```
$ cat .git/HEAD
ref: refs/heads/master
```


## 章节5  查看老版本但不用创建分支 ##
命令`git checkout`通常接一个分支头，但是也可以接一个任意的提交，例如接一个标签：
```
$ git checkout v2.6.17
Note: checking out 'v2.6.17'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at 427abfa... Linux v2.6.17
```

**HEAD**现在指向提交的**SHA-1**而不是分支，并且`git branch`显示你已经不再分支上面了：
```
$ cat .git/HEAD
427abfa28afedffadfca9dd8b067eb6d36bac53f
$ git branch
* (detached from v2.6.17)
  master
```

在这种情况下我们说**HEAD**是分离的。

这是一个很简单的方法，检出一个特定的版本但不用可新分支起名字。你也可以对这个版本创建分支或者标签。


## 章节6  查看远程仓库的分支 ##

克隆是创建的**master**分支是那个仓库的**HEAD**的拷贝。那个远程仓库可能有其他的分支，尽管如此，你的本地仓库保留了跟踪远程仓库的那些远程分支的本地分支，称为*远程跟踪*分支，可以通过命令`git branch -r`查看：
```
$ git branch -r
  origin/HEAD
  origin/html
  origin/maint
  origin/man
  origin/master
  origin/next
  origin/pu
  origin/todo
```

在这个例子中，**origin**称为远程仓库，简称为*远程*。从我们的视角来看这个仓库的分支称为远程分支。远程跟踪分支实在克隆远程仓库是基于浙西远程分支创建的，可以通过命令`git fetch`，`git pull`和`git push`来更新。

你或许想从远程跟踪分支建立一个自己的分支，就像从标签建立新分支一样：
```
$ git checkout -b my-todo-copy origin/todo
```



## 章节7  命名分支，标签或者其他的引用 ##
分支，远程跟踪分支还有标签都是对提交的引用。所有的引用都是以**refs**开头以**/**分隔的路径名。我们至今使用的名字都是简写：

 - 分支**test**就是**refs/heads/test**
 - 标签**v2.6.18**就是**refs/tags/v2.6.18**
 - **origin/master** 就是**ref/remotes/origin/master**

新建的引用都存放在**.git/refs**目录下面，在它们的名字对应的目录下面。然而为了高效的原因它们也可能被存放到单个文件中。

另外一个有用的简写，一个仓库的**HEAD**可以通过仅仅使用仓库的名字来引用。例如**origin**通常是仓库**origin**的HEAD分支的简写。



## 章节8  使用`git fetch`更新仓库 ##
某些情况下你可能在更新前需要检查远程仓库的情况。

命令`git-fetch`不带参数，会更新所有的远程跟踪分支到最新的版本。它不会触及到你所拥有的分支，即便是克隆时创建的**master**分支。

## 章节9  从其他的仓库获取分支 ##
你也可以跟踪不是你克隆的哪些仓库的分支，使用命令`git-remote`:

```
$ git remote add staging git://git.kernel.org/.../gregkh/staging.git
$ git fetch staging
...
From git://git.kernel.org/pub/scm/linux/kernel/git/gregkh/staging
 * [new branch] 		master 		-> staging/master
 * [new branch] 		staging-linus -> staging/staging-linus
 * [new branch] 		staging-next -> staging/staging-next
```

新的远程跟踪分支会存放在你使用`git remote add`命令添加的简写名称的路径下面，在`staging`例子中：

```
$ git branch -r
  origin/HEAD -> origin/master
  origin/master
  staging/master
  staging/staging-linus
  staging/staging-next
```

如果你稍后运行命令`git fetch <remote>`，与**remote**对应的远程跟踪分支就会被更新。

如果你查看**.git/config**就可看到新添加的一段：

```
$ cat .git/config
...
[remote "staging"]
	url = git://git.kernel.org/pub/scm/linux/kernel/git/gregkh/staging.git
	fetch = +refs/heads/*:refs/remotes/staging/*
...
```


# Part II. 探索Git历史 #

## 章节10  如何使用二分查找找到一个退化 ##
假定工程的版本v2.6.18有效，但是**master**版本却崩溃了。有时候找到这种退化的原因的最好的办法就是穷举这个工程的历史来找到产生这个问题的特定的提交。命令`git-bisect`可以帮助你这么做：

```
$ git bisect start
$ git bisect good v2.6.18
$ git bisect bad master
Bisecting: 3537 revisions left to test after this
 [65934a9a028b88e83e2b0f8b36618fe503349f8e] BLOCK: Make USB storage depend on SCSI rather than selecting it [try #6]
```

如果在此时你运行`git branch`命令，就会发现Git暂时吧你置于**no branch**状态。**HEAD**现在和任何其他的分支都分离了，直接指向**master**可以存取的提交（提交ID为 65934a9），但是v2.6.18却不可以存取的提交。编译并测试它，看看是否依然崩溃，假定它崩溃了，然后：

```
$ git bisect bad
Bisecting: 1769 revisions left to test after this
[7eff82c8b1511017ae605f0c99ac275a7e21b867] i2c-core: Drop useless bitmaskings
```

会检出一个更老的版本，继续这样的步骤，告诉Git它给你的版本是好的还是坏的，需要注意每次剩余测试的版本个数都是之前的一半。

在经过13次测试后，它会输出导致错误的提交ID，你可以通过命令`git show`查看是谁写的，然后通过邮件报告你发现的bug并附带这个提交ID。最后需要运行如下的命令返回到之前的分支上：

```
$ git bisect reset
```

需要注意的是`git bisect`检出的版本只是一种建议，你完全可以自己指定版本，你也可以在命令行下运行，例如：

```
$ git bisect visualize
```

在命令行下需要先命令`git reset --hard  commitID`然后在使用`bisect good`或者`bisect bad`来标注那个提交ID是好的还是坏的。例如：
```
$ git reset --hard fb47ddb2db
```
在编译、测试之后使用命令`git bisect good`或者`git bisect bad`来标注这个提交是好的还是坏的。

重复这样的步骤，逐渐缩小要测试的提交的范围，如果跳出当前这个提交ID，可以使用命令：
```
$ git bisect skip
```

然而在这种情形下， Git 可能最终无法在某些首次跳过的提交和以后的错误提交之间分辨出第一个错误。



