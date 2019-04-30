[git教程url](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
## 添加新创建的文件
add命令只是把文件修改提交到git版本库被成为stage的缓存区，commit才是提交修改到版本库/r/n
所以git的提交分两步 add commit

```
//添加单个文件
git add test.txt
//添加当前文件夹下所有新创建的文件
git add .
//提交时同时添加 前提是被改动文件已经是tracked
git commit -am 'test'
```

## 提交到本地库

```
//m里的内容是提交的说明，git不允许空
git commit -m 'test'
```

## 提交到远程仓库

```
git push
```
## 创建版本库

```
mkdir test
git init
```

## 查看仓库当前状态

```
git status
```
## 查看未提交的修改

```
git diff
//查看readme.txt在工作区和版本库里面最新版本的区别
git diff HEAD -- readme.txt
```
## 查看修改记录

```
//显示提交时间、说明、版本号、提交人等
git log
//一行显示，只显示版本号和说明
git log --pretty=oneline
```
## 版本回退

```
//回退到上一个版本 HEAD表示最新版本,HEAD^表示上一个，两个^表示上两个，太多个版本比如上100个用HEAD~100
git reset --hard HEAD^
//回退到指定版本号，版本号没必要写全，前几位就可以了，Git会自动去找，只要能找到唯一的就行
git reset --hard a204bc4cbbf8faca282116755d7847c4d38e1484
```
## 本地版本库操作记录查询
当git版本回滚之后通过git log查看到的最新日志只有回滚后的版本号，回滚前的最新记录会丢失，此时结合reflog命令还可以找到回滚没了的版本号
即通过reset回滚了之后又想恢复之前的版本，可以通过reflog找到之前的版本ID


```
git reflog
```

## 撤销修改
### 撤销未commit的
撤销未push的修改有两种情况，一种撤销工作空间的修改，一种是撤销暂存区的修改

如果已经push了，改了重新add commit push吧
1. 撤销工作空间的修改
适用于一种是修改还没add，此时会从版本库中拉取文件，另一种是已经add但没有commit，此时会从暂存区中拉取文件
```
//--命令很重要，没有--就办成了切换分支
git checkout -- readme.txt
```

2. 撤销暂存区的修改
如果修改已经add了，此时checkout会从暂存区拉取文件，当想从版本库中恢复文件时，就需要用到reset命令

reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本
```
//相当于撤销add，reset后可使用checkout来从版本库拉取文件到工作空间
git reset HEAD readme.txt
```

### 撤销已经commit或push的
其实就是撤销commit，如果已经push了，则再进行强制push就行了

撤销commit分两种，一种reset，不会保留修改的代码，一种revert，会保留原代码并生成新的提交
* revert 是放弃指定提交的修改，但是会生成一次新的提交，需要填写提交注释，以前的历史记录都在。
* reset是指将HEAD指针指到指定提交，历史记录中不会出现放弃的提交记录。

```shell
# 回退到指定版本，不保留原更改代码
git reset --hard e377f60e28c8b84158
# 回退到指定版本，保留原更改代码，且生成新的提交
git revert e377f60e28c8b84158
```
强制提交 -f

```shell
git push -f origin master
```

## 添加远程仓库
远程仓库分为https和ssh，ssh的话需要在git仓库里添加本机的ssh公钥，https的话需要设置你的用户名和密码

此命令适用于先创建的本地仓库，之后需要提交新项目到远程仓库
```
git remote add origin git@git.oschina.net:yuandalong/learning-git.git
```
如果远程仓库已存在，需要拉取代码到本地的话使用clone命令

```
git clone git@github.com:michaelliao/learngit.git
```

## 把修改推送到远程仓库
第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令

```
//-u会把本地master和远程的master关联起来，用于第一次提交
git push -u origin master
//非第一次提交时可省略-u  把本地master分支的最新修改推送至Git
git push origin master
```

## 创建本地分支

### 第一种方法
```
//创建dev分支，-b参数表示创建并切换，相当于branch和checkout两个命令的结合
git checkout -b dev
```
### 第二种方法
```
//创建dev分支
git branch dev
..切换到dev分支
git checkout dev
```

## 创建远程分支
分两步：
1. 创建本地分支
2. 提交本地分支到新的远程分支
3. 创建本地分支和远程分支的关联
```shell
#创建本地分支
git checkout -b dbg_lichen_star
#提交本地分支到指定的新的远程分支
git push origin dbg_lichen_star:dbg_lichen_star
#关联远程和本地分支
git branch --set-upstream-to=origin/dbg_lichen_star dbg_lichen_star
```

## 查看当前分支
会列出所有分支，当前分支会在前面加*

```
git branch
//查看远程仓库所有分支，如果要包含远程仓库刚创建的分支，需要先pull
git branch -a
```
## 切换分支
切换分支不光可以切换本地分支，也可以切换远程分支，通过git branch -a查看远程分支，新创建的分支看不到的需要先pull

```
//切换到master分支
git checkout master
//切换到dev分支
git checkout dev
```
## 合并分支
merge命令来合并分支，此时当没有冲突时git会自动使用Fast forward模式来合并分支，Fast forward模式有个问题是合并后在git log中体现不出曾经做过合并，所以可以用--no-ff参数来禁用Fast forward模式
```
//把dev分支合并到当前工作空间的分支
git merge dev
//禁用Fast forward模式的合并，因为本次合并要创建一个新的commit，所以加上-m参数
git merge --no-ff -m "merge with no-ff" dev

```
## 删除分支

```
//删除dev分支
git branch -d dev
```

## 解决冲突
git解决冲突也只能通过编辑冲突文件之后提交的方式

Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容

通过status命令可以查看冲突文件
```
$ git status
# On branch master
# Your branch is ahead of 'origin/master' by 2 commits.//这行是说有两个没有push的修改
#
# Unmerged paths:
#   (use "git add/rm <file>..." as appropriate to mark resolution)
#
#       both modified:      readme.txt//冲突文件
#
no changes added to commit (use "git add" and/or "git commit -a")
```

用git log --graph命令可以看到分支合并图

```
git log --graph --pretty=oneline --abbrev-commit
```

## 暂存代码 stash


```
1、暂存当前分支代码
git stash
2、查看工作区状态，确保是clean的，需确认新增的文件也add了
git status
3、切换到master分支
git checkout master
4、打个用来修复bug的分支
git checkout -b bugfix
5、修改代码，add commit
git add .
git commit -m 'fix some bug'
6、切换到主干
git checkout master
7、合并分支到主干
git merge --no-ff -m "bug fix" bugfix
8、push到远程版本库
git push 
9、删除bugfix分支
git branch -d bugfix
10、切换到开发分支
git checkout dev
11、查看工作空间暂存列表
git stash list
12、恢复暂存的内容到工作空间
两种方法， 
git stash apply不会删除stash内容，需要用git stash drop来删除
git stash pop 恢复的同时删除stash内容

可以多次stash，恢复的时候，先用git stash list查看，然后恢复指定的stash，用命令：
git stash apply stash@{0}
```

## 丢弃一个没有被合并过的分支,注意是大写的D参数，合并过的分支删除用的是d参数，注意大小写区别，慎用D

```
git branch -D <name>
```
## 查看远程库信息

```
git remote -v
```
## 在本地创建和远程分支对应的分支，本地和远程分支的名称最好一致

```
git checkout -b branch-name origin/branch-name
```
## 建立本地分支和远程分支的关联

```
#貌似已失效了，建议用带to的，注意两个命令本地和远程分支顺序的区别
git branch --set-upstream branch-name origin/branch-name
git branch --set-upstream-to=origin/branch-name branch-name
```
## 标签相关
git的标签对应的是一个版本号

```
//打标签v1.0到最后一次的commit上
git tag v1.0
//打标签v0.9到版本号6224937对应的commit上
git tag v0.9 6224937
//查看所有标签，注意，标签不是按时间顺序列出，而是按字母排序的
git tag
//查看标签信息
git show v1.0
//创建标签时添加说明
git tag -a v0.1 -m "version 0.1 released" 3628164
//删除标签 因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除
git tag -d v1.0
//推送标签到远程仓库
git push origin v1.0
//一次性推送全部尚未推送到远程的本地标签
git push origin --tags
//删除远程仓库标签，需先删除本地，然后从远程删除
git tag -d v1.0
git push origin :refs/tags/v1.0

```

## 服务端相关
添加信任需确认家目录下.ssh目录是700权限

创建远程仓库

```
git init --bare sample.git
```
如果远程仓库不是创建在系统用户家目录，需要建软链
如git clone git@serverIp:git/sample.git,冒号后面对应的是服务器目录，所有目录要真实存在的，可以理解为ssh git@serverIp后进入git/sample.git，所以对应的git的家目录下要有git/sample.git目录



## git本地关联远程项目

1.     选择目录
          进入本地需要关联的目录（比如demo目录），然后git init
 
1.     关联,origin后面的git地址从git远程复制
          git remote add origin git@git.oschina.net:yourname/demo.git
 
1.     更新
           git pull
 
可能会出现的问题：
     如果第二步写错了：则
       git remote rm origin   //删除origin
       git remote add origin git@git.oschina.net:yourname/demo.git   //重新添加origin
 
     如果提示没有权限更新：则
       在git远程上添加本地的公共秘钥.
　　 window本地公共秘钥地址：C:\Users\Administrator\.ssh\id_rsa.pub
　　 linux本地公共秘钥地址：~/.ssh/

## 配置用户名和邮箱

```
//global为全局的，local为当前项目的,system为当前系统用户的，local必须在git目录下执行
git config --global user.name "youname"
git config --global user.email "youeamil@email.com"
//查看用户名和邮箱
git config -l
```

## 修改关联的远程仓库地址
方法有三种


1.修改命令


```
git remote set-url origin [url]
```


2.先删后加


```
git remote rm origin
git remote add origin [url]
```


3.直接修改config文件

修改完成后需要push本地所有分支到远程

## 更新分支列表

```shell
git remote update origin --prune
```

## git status中文8进制显示解决办法

```shell
git config --global core.quotepath false
```

## linux 升级git版本

```shell
yum -y remove git

yum -y install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel

wget https://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.14.tar.gz
./configure -prefix=/usr/local/libiconv && make && make install

wget https://www.kernel.org/pub/software/scm/git/git-2.10.0.tar.gz
make prefix=/usr/local/git all
make prefix=/usr/local/git install

ln -s /usr/local/git/bin/git /usr/bin/git
```

## 文件名大小写 git mv
git默认对文件名大小写不敏感，所以如果只是把文件名大小写变更了，会导致无法将新文件推送到远程仓库，解决办法：
1. git mv oldName newName
    推荐方案
2. git config core.ignorecase false
    不推荐，此方案虽然修改文件名大小写时会有git提示，但是只是会推送新文件，而旧文件在远程仓库上并不会被删除