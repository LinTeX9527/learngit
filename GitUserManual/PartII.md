
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



## 章节11  给提交命名 ##
我们已经见过多种给提交明明的方法：

- 40位字符表示对象名字
- 分支名字，指向指定分支排行之首的提交
- 标签名，指向由指定标签指向的提交
- HEAD，指向当前分支的排行之首

还有其他的方法，如下；
```
$ git show fb47ddb2 # 对象名字的前几个字符，只要是唯一的就可以
$ git show HEAD^  	# HEAD提交的父辈
$ git show HEAD^^ 	# HEAD提交的父父辈
$ git show HEAD~4 	# 往上第4代父辈
```

在合并提交上使用可能会有多个父辈。默认情况下使用`^`和`~`会显示提交的第一代父辈，但是可以用数字指定第几代：
```
$ git show HEAD^1	# 显示HEAD提交的第一代父辈
$ git show HEAD^2 	# 显示HEAD提交的第二代父辈
```

除了使用HEAD，对于提交还有其他几种特别的名字。合并，还有其他的操作，例如`git reset`，会改变当前检出的提交，通常是把**ORIG_HEAD**设置为当前操作之前HEAD指向的提交。

`git fetch`操作总是把最近一次取得的分支的头保存在**FETCH_HEAD**中。例如你运行命令`git fetch`却没有指定一个本地分支作为此操作的目标，例如：
```
$ git fetch git://example.com/proj.git theirbranch
```
取得的提交依然可以通过**FETCH_HEAD**访问。

当提到合并时我们就会看到一个特殊的名字**MERGE_HEAD**，它指向我们想要合并到当前分支的另外一个分支。

命令`git-rev-parse`是一个低层级的命令，当针对这个提交转换它的对象的名字时很有用：
```
$ git rev-parse origin
e05db0fd4f31dde7005f075a84f96b360d05984b
```


## 章节12  创建标签 ##
我们也可以为一个特定的提交创建一个标签，如下：
```
$ git tag stable-1 1b2e1d63ff
```
可以使用**stable-1**来指向这个提交**1b2e1d63ff**。

这样创建一个轻量级的标签。如果你想在创建标签时包含一个注释，并且加密，你可以创建一个标签对象。

## 章节13  浏览历史 ##
命令`git-log`会显示历史列表。就其本身而言，它会显示父辈提交所能追溯到的所有提交，但是你可以发出特殊的请求：
```
$ git log v2.5..		# 从v2.5之后所有的提交，但是不包括v2.5这个提交，即 (v2.5, HEAD]

$ git log test..master	# 列出从test到master之间的提交，不包括test，但是包括master即(test, master]

$ git log master..test	# 列出test可以访问的提交，但是master不能访问的提交，即(master, test]

$ git log master...test	# 列出master和test能够单独访问的提交，但是不能是二者同时访问的提交，即master和test的对称差

$ git log --since="2 weeks ago" 	# 列出最近2周的提交

$ git log Makefile      # 列出包含更改文件Makefile的所有提交

$ git log fs/			# 列出包含更改目录fs/下面任何文件的所有提交

$ git log -S'foo()'		# 列出文件中所有关于字符串'foo()'的增加、删除的提交

```

当然你可以使用任意的组合，例如下面这个命令列出从v2.5之后所有更改*Makefile*或者*fs/*目录下面的文件的所有的提交：
```
$ git log v2.5.. Makefile fs/
```

还可以使用如下命令来列出补丁：
```
$ git log -p
```

注意到**git log**会列出最近的提交然后反向追踪到父辈提交，因为Git历史可能包含多个开发中的独立分支，实际列出的提交的顺序在某种程度上是随意。


## 章节14  产生差异 ##
使用命令`git-diff`产生两个版本之间的差异：
```
$ git diff master..test
```
这个命令会产生两个分支断点的差异。如果你想要找到它们共同的祖先和test之间的差异，需要使用3个点：
```
$ git diff master...test
```

某些时候你可能需要的反倒是补丁文件，可以使用命令`git-format-patch`:
```
$ git format-patch master..test
```
上面的命令会产生一个补丁文件，包含(master, test]之间各个提交的差异。



## 章节15  查看老文件版本 ##
你总是可以通过先检出正确的版本来查看老文件版本。但是有时候有更方便的方法来检查单个文件的老版本却不用检出任何东西，这个命令如下：
```
$ git show v2.5:fs/locks.c
```
冒号之前的可以是任何命名的提交，之后是Git跟踪的文件路径。

## 章节16 示例 ##
### 统计某个分支上的提交个数 ###
假设你想要统计从**origin**分支上开辟的新分支**mybranch**上提交的个数，
```
$ git log --pretty=oneline origin..mybranch | wc -l
```


### 检查两个分支是否指向同一个历史 ###
使用如下命令可以检查两个分支是否指向同一个历史：
```
$ git diff origin..master
```
当然你也可以比较它们的对象名字：
```
$ git rev-list origin
e05db0fd4f31dde7005f075a84f96b360d05984b
$ git rev-list master
e05db0fd4f31dde7005f075a84f96b360d05984b
```

除此之外你也可以使用操作符`...`来选择所有的某个分支能访问的提交但是另一个分支不能访问的提交，即对称差：
```
$ git log origin...master
```

### 找到包含特定修复的第一个打标签的版本 ###
假设提交*e05db0fd*修复了某个问题，你想要找到找到包含这个修复的第一个打标签的发布版本。
当然，可能有多种答案--如果在提交之后有了多个分支，就可能有多个**最早**的打标签的发布版本。

你可以肉眼查找提交*e05db0fd*之后的所有提交：
```
$ gitk e05db0fd..
```

或者你可以使用命令`git-name-rev`，它会找到这个提交的所有后代中具有标签的某个：
```
$ git name-rev --tags e05db0fd
e05db0fd tags/v1.5.0-rc1^0~23
```

#### TODO ####
部分省略。


### 为软件发布版本创建一个changelog并且打包 ###
命令`git-archive`会对工程的任意一个版本创建打包文件，示例如下：
```
$ git archive -o latest.tar.gz  --prefix=project/ HEAD
```

会使用*HEAD*产生一个压缩文件，其中的每一个文件名字都添加了前缀`project/`。如果可能的话会使用输出文件的扩展名作为输出文件的格式。


