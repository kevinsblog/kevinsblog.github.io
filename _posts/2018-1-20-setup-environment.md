---
layout: post
title: Git使用
tags: [Linux, Shell, Git]
bigimg: /img/path.jpg
comments: true
---

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
创建本地SSH密钥，GitHub需要用它来识别推送的提交时由授权用户发出的，
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

## 工作区与暂存区

隐藏目录.git是Git的版本库，版本库中最重要的是暂存区(stage, index)，还有Git创建的分支，master，以及指向master的指针HEAD。
![工作区和暂存区](https://static.liaoxuefeng.com/files/attachments/919020037470528/0)

git add 将将文件修改添加到暂存区，git commit将暂存区的所有内容提交到当前分支。

Git跟踪并管理的是修改(增加删除字符，增加删除文件)，而不是文件，git add命令中，工作区修改被存入暂存区。

修改和提交的一般流程是，
第一次修改 -> git add -> 第二次修改 -> git add -> git commit
如果修改不加入暂存区，是不会跟着git commit提交上去的。

```
git diff HEAD -- readme.txt

>diff --git a/readme.txt b/readme.txt
index 22c0582..7dca198 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,3 +1,4 @@
 Git is a distributed version control system.
 Git is free software under the GPL.
 Git has a mutable index called stage.
+Git tracks changes
```

撤销修改，若修改还没有被放到暂存区，文件回到和版本库一模一样，已经添加到暂存区的修改，撤回添加暂存区前的状态。
```
git checkout -- readme.txt   //丢弃工作区未提交的修改
git status

>On branch master
nothing to commit, working tree clean
```
若要丢弃添加到暂存区的修改。
```
git status

>On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   readme.txt

git reset HEAD readme.txt
git checkout -- readme.txt
git status

>On branch master
nothing to commit, working tree clean
```
已经提交的修改，要撤销需要版本回退。

## 删除文件

添加一个文件
```
touch test.txt
git add .
git commit -m 'add new text test'
```

从本地及库上删除文件
```
rm test.txt
git status

>On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")

git rm test.txt
> On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	deleted:    test.txt

git commit -m "remove text test"
```
还原误删的文件
```
git checkout -- test.txt
```

# Git远程仓库和GitHub

Git是分布式版本控制系统，同一个Git仓库可以分布到不同的机器上，一台机器上有一个原始版本库，别的机器可以克隆这个版本库，每台机器的版本库都一样，没有主次之分。

实际情况下，一般是找一台电脑充当服务器，每个人都从服务器仓库克隆一份到本地，再各自把提交推送到服务器仓库，也可以从服务器拉去别人的提交。

GitHub提供了Git仓库的托管服务

## 添加远程仓库

登录GitHub，创建名为learngit的仓库，关联本地仓库和远程仓库，origin是默认的远程库名，
```
git remote add origin git@github.com:kevinsblog/learngit.git
```
把本地库的内容推送到远程库，把当前分支master推送到远端。第一次推送时，加上-u参数，Git把本地master分支和远程master分支关联起来
```
git push -u origin master  //第一次推送
git push origin master  //后续推送
```

# Git分支管理

分支是两个平行宇宙。假设在开发一个新功能，需要一段时间，在功能未完成前，若提交代码会造成代码库不完整，其它人无法干活，但是如果代码留到完成后才一次提交，又存在丢失进度的巨大风险。

因此可以创建一个属于自己的分支，别人看不到，继续在原来的分支上工作，而自己在自己的分支上工作，该提交就提交，直到开发完毕，最后一次性合并到原来的分支上。

## 创建和合并分支

每次提交，Git会把它们串成一条时间线，时间线是一条分支，如master分支。HEAD指向当前分支。
```
    HEAD
        \
        master
          |
O -> O -> O
```

创建新分支dev，再把HEAD指向dev，将当前分支改为dev，再向dev分支提交。dev指针往前移动一步，master指针则不变。合并分支时，将master指向dev的当前提交，就完成了合并。
```
git checkout -b dev //创建并切换
git branch  //查看当前分支

>* dev
  master

cat readme.txt
>Git is for version ctrl
Add branch Dev
```
或者
```
git branch dev
git checkout dev
```
切换分支后，利用git add和git commit提交代码
切换回master分支
```
cat readme.txt 

>Git is for version ctrl
Add branch Dev

git checkout master

>M	readme.txt
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.

cat readme.txt 
>Git is for version ctrl
```
切换回master分支后，只在dev分支上提交的代码就看不到了。
```
        master
          |
O -> O -> O -> O
               |
              dev
                \
                 HEAD
```
合并分支时，将master指向dev的当前提交，就完成了合并。合并后删除dev分支，就是把dev的指针删掉。
```
git merge dev //将dev分支的工作合并到master分支

>Updating aeb092e..d0451db
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)

git branch -d dev //合并dev分支后删除dev分支

>Deleted branch dev (was d0451db).

git branch

>* master
```
git merge合并指定分支(dev)到当前分支(master)
```
          HEAD
             \       
            master
               |
O -> O -> O -> O
```

## 解决分支冲突

分支合并之前可能要先解决冲突，
```
git checkout -b feature1

//添加新行 “feature1 branch work”
```
切换到master分支
```
git checkout master

>Switched to branch 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

//添加新行 “master branch work"
```
两个分支各有新的提交，快速合并是无法执行的，会检测到冲突
```
O - O - O - O (master)
         \_ O (feature1)
```
```
git merge feature1

>Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.

git status

>On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
查看冲突，++<<<<<<<和++>>>>>>>标注了不同分支的内容。
```
git diff readme.txt 

>--- a/readme.txt
+++ b/readme.txt
@@@ -1,3 -1,3 +1,7 @@@
  Git is for version ctrl
  Add branch Dev
++<<<<<<< HEAD
 +master branch work
++=======
+ feature1 branch work
++>>>>>>> feature1
```
编辑文件后再提交，查看分支合并过程。
```
git log --graph --pretty=oneline --abbrev-commit

>*   36f9e7b merge
|\  
| * 0afe918 feature1
* | a294971 master
|/  
* d0451db add one line in dev
* aeb092e readme
* 513a369 Initial commit
```
删除feature1分支
```
git branch -d feature1

>Deleted branch feature1 (was 0afe918).
```

## No Fast Forward分支管理

在合并时，默认会用Fast forward模式，在这种模式下，删除分支后，分支信息就丢失了。

不用Fast forward模式，在合并时会生成一个新的commit，在分支历史可以看出分支信息。
```
$ git checkout -b dev
$ git add readme.txt
$ git commit -m "add merge"

[dev 97f684d] add merge
 1 file changed, 1 insertion(+)
```
完成后切换分支，准备合并，禁用Fast forward，最后查看分支历史。
```
$ git checkout master
$ git merge --no-ff -m "merge with no-ff" dev

Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)

$ git log --graph --pretty=oneline --abbrev-commit

*   55af3bb merge with no-ff
|\  
| * 97f684d add merge
|/  
*   36f9e7b merge
```
在禁用Fast forward后，分支合并就像，
```
                  master - HEAD
                 /
O - O - O - O - O
          \   /
            O (dev)
```

## 分支策略

分支管理的几个基本原则是，

* master分支是稳定的，仅用来发布新颁布，平常不用了提交
* dev分支是不稳定的，用于日常工作
* 每个人都在dev分支上工作，拥有自己的分支，提交合并到dev分支上

![团队分支](https://static.liaoxuefeng.com/files/attachments/919023260793600/0)

## Bug分支

bug可以通过一个临时的分支来管理修复，修复后合并分支，删除临时分支。

比如，临时接到修复bug101的任务，但是dev分支还没有完成提交，可以用git stash将工作现场储藏起来，后续恢复现场
```
$ git status
$ git stash
```
在master分支上修复bug101
```
$ git checkout master
$ git checkout -b issue-101
```
修复完成后，切换回master分支，进行合并
```
$ git checkout master
$ git merge --no-ff -m "merged bug fix 101" issue-101
```
回到dev分支工作
```
$ git checkout dev
$ git status
$ git stash list
$ git stash pop
$ git stash apply stash@{0} //恢复到特定的现场
```