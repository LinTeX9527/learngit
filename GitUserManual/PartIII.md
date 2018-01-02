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

## 章节18 创建新的仓库 ###
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

