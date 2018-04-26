---
title: 学习Git看这一篇就够
date: 2018-04-26 14:27:44
tags: Git
categories: Git
---
### 前言
上大学那会儿都流行使用SVN，直到工作后才真正接触并使用Git，发现Git真是个好东西，相见恨晚。一直想写一篇关于Git使用的文章，但是网上类似的教程有很多，所以就懒得写了，平时碰到问题随手上网一查然后记录在印象笔记上，久而久之，记录的越来越多，发现平时工作中用到的无非那么几个指令，所以把这些常用指令整理了下，只要搞懂下面这些指令，基本上能应付工作中90%的Git场景。
<!-- more -->

### Git配置

**1.建立本地仓库**
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo
$ git init
Initialized empty Git repository in D:/GitDemo/.git/
```

**2.配置用户名和邮箱**  
每次git提交时都会引用用户名和邮箱，随更新内容一起被纳入历史记录。若用了[--global]参数则修改的是用户主目录下的配置文件，所有项目会默认使用该配置文件；若需要在某项目中使用特定的用户名和邮箱，不加[--global]参数重新配置即可，配置文件保存在当前项目的.git目录下。
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo (master)
$ git config --global user.name "kaijianchen"
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo (master)
$ git config --global user.email "kaijianchen@pptv.com"
```

查看配置结果  
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo (master)
$ git config --list
user.name=kaijianchen
user.email=kaijianchen@pptv.com
...
```

试着提交一笔记录，然后使用git log查看提交记录时能看到提交人用户名和邮箱。  
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo (master)
$ git log
commit 82e807ae6684ee5b5debf01fb94fe02d27530df9 (HEAD -> master)
Author: kaijianchen <ckj375@gmail.com>
Date:   Wed Apr 25 17:11:25 2018 +0800
    添加文件
```

**3.本地仓库导出及克隆**  
将本地Git仓库导出为裸仓库（即不包含工作区和暂存区的仓库，只包含commit提交后的数据）
```
kaijianchen@WIN7-1712111933 MINGW64 /d
$ git clone --bare GitDemo GitDemo.git
Cloning into bare repository 'GitDemo.git'...
done.
```
克隆本地仓库
```
kaijianchen@WIN7-1712111933 MINGW64 /d
$ git clone GitDemo.git GitDemo1
Cloning into 'GitDemo1'...
done.
```

### 远程仓库相关命令

**1.克隆远程仓库**  
git clone &lt;url&gt; [shortname]  
```
kaijianchen@WIN7-1712111933 MINGW64 /d
$ git clone git@github.com:ckj375/GitDemo.git GitDemo1
Cloning into 'GitDemo1'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
```
**2.查看当前远程仓库地址**
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git remote -v
origin  git@github.com:ckj375/GitDemo.git (fetch)
origin  git@github.com:ckj375/GitDemo.git (push)
```
**3.移除远程仓库**  
git remote rm &lt;name&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git remote rm origin
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git remote -v
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
```

**4.添加远程仓库及更新推送**  
git remote add &lt;name&gt; &lt;url&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git remote add origin git@github.com:ckj375/GitDemo.git
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git remote -v
origin  git@github.com:ckj375/GitDemo.git (fetch)
origin  git@github.com:ckj375/GitDemo.git (push)
```
git pull &lt;name&gt; &lt;branch&gt; --allow-unrelated-histories
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git pull origin master --allow-unrelated-histories
From github.com:ckj375/GitDemo
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master
Already up to date.
```
 git push -u &lt;name&gt; &lt;branch&gt;
 ```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git push -u origin master
Everything up-to-date
Branch 'master' set up to track remote branch 'master' from 'origin'.
```
**5.添加多个远程仓库及推送**  
git remote add &lt;name&gt; &lt;url&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git remote add origin git@github.com:ckj375/GitDemo.git
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git remote add origin1 git@github.com:ckj375/GitDemo_copy.git
```
查看当前所有远程库地址
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git remote -v
origin  git@github.com:ckj375/GitDemo.git (fetch)
origin  git@github.com:ckj375/GitDemo.git (push)
origin1 git@github.com:ckj375/GitDemo_copy.git (fetch)
origin1 git@github.com:ckj375/GitDemo_copy.git (push)
```
将某一本地分支推送到某一远程仓库使用git push -u &lt;name&gt; &lt;branch&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git push -u origin1 master
Counting objects: 8, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (8/8), 1.05 KiB | 359.00 KiB/s, done.
Total 8 (delta 0), reused 0 (delta 0)
To github.com:ckj375/GitDemo_copy.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin1'.
```
### 分支操作相关命令

**1.查看本地分支，远程分支，所有分支**
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git branch
* master
ianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git branch -r
  origin/master
ianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git branch -a
* master
  remotes/origin/master
```
**2.本地分支添加，切换，重命名，删除**  
git branch &lt;branch&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git branch new_branch
```
git checkout &lt;branch&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git checkout new_branch
Switched to branch 'new_branch'
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (new_branch)
```
git branch -m &lt;old branch&gt; &lt;new branch&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (new_branch)
$ git branch -m new_branch new_branch2
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (new_branch2)
```
若要删除某本地分支，须先切到其他分支上，再执行git branch -d &lt;branch&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git branch -d new_branch2
Deleted branch new_branch2 (was d4c56f5).
```
**3.本地分支push到远程，远程分支删除及重命名**  
git push &lt;name&gt; &lt;branch&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git push origin new_branch
Total 0 (delta 0), reused 0 (delta 0)
To github.com:ckj375/GitDemo.git
 * [new branch]      new_branch -> new_branch
```
删除远程分支使用git push &lt;name&gt; --delete &lt;branch&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git push origin --delete new_branch
To github.com:ckj375/GitDemo.git
 - [deleted]         new_branch
```
重命名远程分支其实就是先删除远程分支，然后重命名本地分支并push到远程。
```
git push origin --delete old_branch
git branch -m old_branch new_branch
git push origin new_branch
```
**4.分支合并**  
merge 是一个合并操作，会将两个分支的修改合并在一起，默认会提交合并中修改的内容。  
rebase 其实并没有进行合并操作，只是提取了当前分支的修改，并将其复制到目标分支的最新提交后面。  
```
A — B — C            master
         \
          D — E      new_branch
```
git merge &lt;branch_name&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git merge new_branch
```
将new_branch分支merge至master分支
```
A — B — C ————— F    master
         \     /
          D — E      new_branch
```
git rebase &lt;branch_name&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git rebase new_branch
```
将new_branch分支rebase至master分支
```
A — B — C — D' — E'  master
         \
          D — E      new_branch
```
### 文件操作相关命令

**1.查看文件修改状态**
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git status
On branch master
Your branch is ahead of 'origin1/master' by 1 commit.
  (use "git push" to publish your local commits)
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
        modified:   README.md
        deleted:    README2.md
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README3.md
no changes added to commit (use "git add" and/or "git commit -a")
```
**2.查看文件修改内容**  
查看某一文件的具体改动内容使用git diff &lt;文件路径&gt;，若需查看所有文件改动内容则使用git diff
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git diff README.md
diff --git a/README.md b/README.md
index c14ae46..4584689 100644
--- a/README.md
+++ b/README.md
@@ -2,3 +2,5 @@
 Git演示Demo
 Hello World
+
+Hello Android
```
**3.撤销修改**  
撤销某一文件的修改使用git checkout &lt;文件路径&gt; ，若需撤销所有修改（新增文件除外）使用git checkout .
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git checkout README.md
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git checkout .
```
若需删除新增文件则使用rm <文件路径>
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ rm README3.md
```
**4.将修改从工作区添加至暂存区**  
添加某一文件的修改至暂存区使用git add/rm &lt;文件路径&gt;，若需提交所有修改至暂存区则使用git add .
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git rm README2.md
rm 'README.md'
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git add README.md
```
**5.将修改从暂存区移回工作区**  
若需要从暂存区移除某一文件的修改则使用git reset HEAD &lt;文件路径&gt;，若需要移除所有修改，则使用git reset HEAD .
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git reset HEAD test.txt
Unstaged changes after reset:
M       test.txt
```
**6.将修改从暂存区提交至本地仓库**  
git commit  -m "[注释]"
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git commit -m "修改备注"
[master d4c56f5] 修改备注
 1 file changed, 1 insertion(+)
```
**7.查看本地仓库提交记录及commit记录回退**  
查看所有记录使用git log，若需查看最近2条记录则使用git log -2
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git log -2
commit df6e17f2ab6b441340d265af9dd15d625122e599 (HEAD -> master)
Author: chenkaijian <ckj375@gmail.com>
Date:   Mon Apr 23 17:53:54 2018 +0800
    修改README
commit bc2bd1a16199639cd303403f2183d0ea0d0bca60
Author: chenkaijian <ckj375@gmail.com>
Date:   Mon Apr 23 17:48:11 2018 +0800
    修改
```

commit记录回退分为软回退git reset --soft &lt;commit_id&gt;和硬回退git reset --hard &lt;commit_id&gt;，例如当前版本为C，现回退至A版本，如果是软回退，C和A版本间的差异内容会保留在暂存区，如果是硬回退则会彻底删除该部分修改内容。
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git reset --soft bc2bd1a16199639cd303403f2183d0ea0d0bca60
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
        modified:   README.md
        modified:   README2.md
$ git log -2
commit bc2bd1a16199639cd303403f2183d0ea0d0bca60 (HEAD -> master)
Author: chenkaijian <ckj375@gmail.com>
Date:   Mon Apr 23 17:48:11 2018 +0800
    修改
```
**8.对比两次commit记录差异**  
查看commitB相对于commitA修改了哪些内容，可以使用git diff &lt;A_commit_id&gt; &lt;B_commit_id&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git diff d4c56f56420561f777f33d8cbdc2412e804e4df3 477f824a3ef614a4d153cc04d10065146b3c6048
diff --git a/README.md b/README.md
index a69d1cf..1cecf2e 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,4 @@
 # GitDemo
 Git演示Demo
+hello world
```
**9.将修改推送至远程仓库及更新**  
如果当前分支只有一个追踪分支，那么主机名都可以省略；如果当前分支与多个主机存在追踪关系git push -u origin master 上面命令将本地的master分支推送到origin主机，同时指定origin为默认主机， 不带任何参数的git push，默认只推送当前分支。
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git push
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 299 bytes | 149.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:ckj375/GitDemo.git
   430d4b8..d4c56f5  master -> master
```
git pull用法基本同git push，默认只更新当前分支
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git pull
Already up to date.
```
### Tag操作相关命令

**1.本地tag添加，查看，切换，删除**  
git tag -a &lt;tagname&gt; -m "[注释]"
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git tag -a v1.0 -m "version 1.0"
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git tag -a v1.1 -m "version 1.1"
```
git tag
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git tag
v1.0
```
git checkout &lt;tagname&gt;  
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git checkout v1.0
Note: checking out 'v1.0'.
```
git tag -d &lt;tagname&gt;  
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git tag -d v1.0
Deleted tag 'v1.0' (was a861dd0)
```
**2.本地tag推送至远程仓库，远程tag删除**  
将本地tag推送至远程仓库
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git push --tags
Counting objects: 1, done.
Writing objects: 100% (1/1), 161 bytes | 161.00 KiB/s, done.
Total 1 (delta 0), reused 0 (delta 0)
To github.com:ckj375/GitDemo.git
 * [new tag]         v1.0 -> v1.0
```
若需删除远程tag则使用git push &lt;name&gt; --delete tag &lt;tagname&gt;
```
kaijianchen@WIN7-1712111933 MINGW64 /d/GitDemo1 (master)
$ git push origin --delete tag v1.0
To github.com:ckj375/GitDemo.git
 - [deleted]         v1.0
```
### 常见问题汇总

**1.克隆代码仓库提示Filename too long**  
```
git config [--global] core.longpaths true
```
**2.gitignore文件忽略失败**  
.gitignore只能忽略原来没有被track的文件，如果文件已经被纳入了版本管理中，则修改.gitignore是无效的，必须先把本地缓存删除然后再提交。
```
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```
