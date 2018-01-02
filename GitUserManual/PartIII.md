# Part III. 使用Git做开发 #
## 章节17  配置用户名 ##
在做提交之前，必须要告诉Git你的信息。最简单的方式如下：
```
$ git config --global user.name 'Your Name Comes Here'
$ git config --global user.email 'you@yourdomain.example.com'
```
在这两条命令生效之后，你的主目录下面的**.gitconfig**文件中会增加如下条目：
```
 [user]
 	name = Your Name Comes Here
 	email = you@yourdomain.example.com
```

## 章节18  创建新的仓库 ###
从头开始创建一个仓库是非常简单的：
```
$ mkdir project
$ cd project
$ git init
```
如果你已经有了初始化的内容，例如一个压缩文件，可以这样：
```
$ tar xzvf project.tar.gz
$ cd project
$ git init
$ git add . # include everything below ./ in the first commit:
$ git commit
```

## 章节19  如何提交 ##
新建提交需要3个步骤：
 1. 工作目录中做一些修改
 2. 告诉Git你做了哪些修改
 3. 使用步骤*2*中的修改创建新的提交

实际上步骤*1*和步骤*2*可以重复任意多次。为了追踪你在步骤*3*中准备提交的东西，Git在特定的暂存区域创建了一个树中内容的快照，称之为**索引**。

一开始，索引中的内容和**HEAD**中的内容完全一致，可以使用命令`git diff --cached`显示**HEAD**和索引的区别，当然在这个时间点是没有任何输出的。

修改索引非常简单，使用新的文件或者修改来更新索引，使用如下命令：
```
$ git add path/to/file
```
从索引和工作树中删除一个文件，使用如下命令：
```
$ git rm path/to/file
```
每一步操作之后可以使用`git diff --cached`来验证，它总是会显示**HEAD**和索引文件的区别，这个就是你将要提交的内容。

命令`git diff`显示的是工作树和索引文件之间的差异。需要注意的是`git add`总是添加当前文件的内容到索引中；之后对同一个文件的修改会被忽略，直到你对同一个文件再次使用`git add`。
当你准备好了，就使用`git commit`进行提交。

几个经常使用的命令是：
```
$ git diff --cached 	# 显示索引和HEAD之间的差别，这是你将要提交的东西

$ git diff 				# 显示工作目录和索引之间的差别

$ git diff HEAD 		# 显示工作目录和HEAD之间的差别

$ git status			# 工作目录中每个修改文件的简短状态
```

## 章节20  创建好的提交信息 ##
尽管不是必须的，这是一个好主意，提交信息以简短的一行文字（少于50个字符）总结所做的更改，紧接着一个空行，紧接着一个全面的描述。第一个空行上面的文字被视为提交标题，这个标题会在Git中到处使用。


## 章节21  忽略文件 ##
工程经常会产生一些你不希望Git进行跟踪的文件。可以在文件**.gitignore**中告诉Git不需要跟踪哪些文件，例如：
```
# Lines starting with '#' are considered comments.
# Ignore any file named foo.txt.
foo.txt

# Ignore (generated) html files
*.html

# except foo.html which is maintanined by hand.
!foo.html

# Ignore objects and archives.
*.[oa]
```

