---
layout: post
title: Mac/Linux 配置开发环境
subtitle: Git
tags: [Linux, Shell, Git]
bigimg: /img/path.jpg
comments: true
---
**配置开发环境的过程繁琐而易错，每次安装新环境都要花费相当的时间，因此在本文中稍作总结**

* toc
{:toc}

[廖雪峰的Git教程](https://www.liaoxuefeng.com)

# 安装和配置Git服务

## 关于Git

Git是分布式版本控制系统，集中式的控制系统将版本库放在中心服务器上，每次更改要从服务器上取得代码，更改后推送回服务器，好比一个图书馆，修改一本书需要先借出来，修改后还回去。集中式代码控制必须联网才能工作。

![中心化控制](https://static.liaoxuefeng.com/files/attachments/918921540355872/0)

分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，不需要联网工作。两个人同时修改后，只需要将自己的修改推送给对方。分布式控制的安全性更高，不会因为中心服务器崩坏导致代码丢失。分布式控制的服务器只是充当便利“交换”修改的工具。

![分布式控制](https://static.liaoxuefeng.com/files/attachments/918921562236160/0)

## 安装Git

以前存在一个软件GIT(GNU Interactive Tools)，因此git曾经叫git-core。
```
sudo apt-get install git-core
sudo apt-get install git
```
## 设置当前GitHub用户

--global指定了机器上所有的git仓库都会用的配置，也可以为特定的仓库指定配置。
```
git config --global user.name <user id>
git config --global user.email <email>
```
## 配置GitHub SSH密钥
测试GitHub的连接，
```
ssh -T git@github.com
```
创建本地SSH密钥，
```
ssh-keygen -t rsa -C "<email>"
```
在GitHub上添加密钥，
```
cat id_rsa.pub
```
> 依次点击Account settings（右上角倒数第二个图标） -> SSH Keys -> Add SSH Key，将id_rsa.pub文件中的字符串复制进去，注意字符串中没有换行和空格。

再次用第一步的方法测试GitHub的连接。
```
Hi kevinsblog! You've successfully authenticated, but GitHub does not provide shell access.
```

# 使用Git

## 关于版本库(repository)

版本库是一个目录，里面所有的文件都可以被Git管理，文件的修改删除都可以追踪，可以还原。

## 创建版本库

创建版本库目录
```sh
mkdir learngit
cd learngit
pwd
```

将目录变为版本库，目录下多了一个.git文件。
```
git init
ls -ah
```

## 在版本库中添加、修改文件

在目录下添加文件，可以设置默认编码为UTF-8 without BOM
```
-- readme.txt
Git is a version control system.
Git is free software.

nano readme.txt
cat readme.txt
```
告诉Git，将文件添加到仓库。
```
git add readme.txt
git add . //add all files of current dir
```
将文件提交到仓库，-m后跟的是提交的说明。
```
git commit -m "wrote a readme file"
```

将readme.txt修改成
```
Git is a distributed version control system.
Git is free software.
```
查看仓库当前的状态，有文件更改尚未提交。
```
git status

>On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
查看修改了什么内容，readme.txt中有一行存在更改。
```
git diff readme.txt

>diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.
```
提交修改，可以查看提交后目录是否是干净的。
```
git add readme.txt
git status

>On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   readme.txt

git commit -m "add distributed"
git status

>On branch master
nothing to commit, working tree clean
```

## 版本查询和回退

查看提交的历史记录，显示从最近到最远的提交日志。commit id 是用SHA1计算出来的一个非常大的十六进制数字。HEAD -> master标注了当前的版本。
```
git log

>commit c049e8c3a4f0369bdb9f737e2f99f9e67daf71bb
Author: kevinsblog <zhugy92@163.com>
Date:   Fri Jun 21 10:41:06 2019 +0800

    add GPL

commit d0229102b75ff92e8d82a93e02d3b20638178599
Author: kevinsblog <zhugy92@163.com>
Date:   Fri Jun 21 10:38:25 2019 +0800

    add distributed

commit 7462e5ee2be25c8ecbaf9ee4dffc81b7e98dd015
Author: kevinsblog <zhugy92@163.com>
Date:   Fri Jun 21 10:28:51 2019 +0800

    wrote a readme file
```
或者简练一点
```
git log --pretty=oneline

>c049e8c3a4f0369bdb9f737e2f99f9e67daf71bb add GPL
d0229102b75ff92e8d82a93e02d3b20638178599 add distributed
7462e5ee2be25c8ecbaf9ee4dffc81b7e98dd015 wrote a readme file
```
查询当前版本
```
┌────┐
│HEAD│
└────┘
   │
   └──> ○ append GPL
        │
        ○ add distributed
        │
        ○ wrote a readme file
```

回退到上一个版本(HEAD^)，上上一个版本(HEAD^^)
```
git reset --hard HEAD^

>HEAD is now at d022910 add distributed

git log --pretty=oneline

>d0229102b75ff92e8d82a93e02d3b20638178599 add distributed
7462e5ee2be25c8ecbaf9ee4dffc81b7e98dd015 wrote a readme file
```
再回到最新的版本，版本号只需要给出一部分，git会自己去搜索补全。
```
git reset --hard c049e8

>HEAD is now at c049e8c add GPL
```
reflog查看提交历史，总是可以找到未来版本的commit id
```
git reflog

>c049e8c HEAD@{0}: reset: moving to c049e8
d022910 HEAD@{1}: reset: moving to HEAD^
c049e8c HEAD@{2}: commit: add GPL
d022910 HEAD@{3}: commit: add distributed
7462e5e HEAD@{4}: commit (initial): wrote a readme file
```