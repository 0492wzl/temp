---
title: git
date: 2016-03-20 22:18:56
categories: 版本控制
tags: git
---

### 简介
官网：[git-scm.com](http://git-scm.com)  
CVS/SVN：集中式版本控制系统，git:分布式  
优势：不必联网，分支管理强大，速度快。  
教程参考：[廖雪峰的Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000 "git教程")  

### 备注
- 本文中，{}表示自定义内容，[]表示可选内容
- Unix的哲学是“没有消息就是最好的消息”  
- 新建文件夹：`$ mkdir {folderName}`  
- 显示当前目录：`$ pwd`  
- 显示隐藏目录：`$ ls -ah`  
- 显示文件内容：`$ cat {fileName} `
<!--more-->
### 安装
客户端下载：[msysgit](http://msysgit.github.io)  

### 使用
- **配置提交人信息**  
`$ git config --global user.name "[yourname]"`  
`$ git config --global user.email "[email@example.com]"`  
	> 注：--global表示全局，若不加只代笔当前仓库  

- **新建仓库create repository**  
(1) 新建文件夹  
(2) 切换到该文件夹  
(3) `$ git init`

- **添加文件**  
(1) `$ git add {filename}`：添加到暂存区  
	 　`$ git add file1 file2 file3`  
	 　`$ git add .`添加所有

	(2) `$ git commit [filename] -m "{commit message}"`：提交到当前分支  

- **修改文件之后**  
(1) `$ git status`：查看当前状态  
(2) `$ git diff {filename}`：查看修改  

	>$ git diff text2.txt  
	>diff --git a/text2.txt b/text2.txt  
	>index e69de29..95d09f2 100644  
	>--- a/text2.txt  
	>+++ b/text2.txt  
	>@@ -0,0 +1 @@  
	>+hello world  
	>\ No newline at end of file  

	(3) `$ git add {filename}`：添加到暂存区  
	(4) `$ git commit [filename] -m "{commit message}"`：提交到当前分支  

- **显示提交历史**  
`$ git log`  
	>commit c3c4437844df9e836ae93ddb7060bd04b6ecea6c  
	>Author: [zhangsan] <[1234 56789@qq.com]>  
	>Date:   Sun Mar 13 22:31:24 2016 +0800  
    >first commit

	`$ git log --pretty=oneline`
	>ee245d799d56fe03f50a009c6d359becbf578443 first delete  
	>c3c4437844df9e836ae93ddb7060bd04b6ecea6c first commit

- **版本回退**  
上一个版本：`$ git reset --hard HEAD^`  
上上一个版本：`$ git reset --hard HEAD^^`  
往上100个版本：`$ git reset --hard HEAD~100`  
历史某个版本：`$ git reset --hard {commitId}`

- **显示每次操作指令**  
`$ git reflog`  
	>1375f0e HEAD@{0}: commit: add here is if  
	796b34c HEAD@{1}: reset: moving to 796b3  
	66600a7 HEAD@{2}: commit: delete the text1.txt  
	796b34c HEAD@{3}: reset: moving to HEAD^  
	68fca70 HEAD@{4}: commit: delete text1.txt  
	796b34c HEAD@{5}: commit: add words hello world  
	d49f63f HEAD@{6}: commit (initial): first create file text1.txt  

- **查看工作区和版本库里面最新版本的区别**  
`$ git diff HEAD -- {filename}`

- **撤销修改**  
(1)撤销工作区修改  
　`$ git checkout -- {filename}`  
	>一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；  
	一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。  
	总之，就是让这个文件回到最近一次git commit或git add时的状态。

	(2)撤销暂存区修改（unstage）  
　`$ git reset HEAD {filename} `
	>把暂存区的修改撤销掉（unstage），重新放回工作区

	(3)应用场景：  
	>场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。  
	场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。  
	场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

- **删除文件**  
	(1)`$ rm {filename}`  
	> 相当于从工作区中删除，并未git add  

	(2)`$ git rm {filename}`  
	>从工作中删除，并且已经git add

### 远程仓库(github)
- **SSH KEY**  
(1)创建  
　　在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：`$ ssh-keygen -t rsa -C "youremail@example.com"`  
　　`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。  
	 > 为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

	(2)登录github，并且添加自己的key  
	>打开“Account settings”，“SSH Keys”页面：然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容。点“Add Key”，你就应该看到已经添加的Key。

- **远程仓库**  
(1)把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库
　`$ git remote add origin git@github.com:{username}/{respName}.git`
	>添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。　　

	(2)把本地库的所有内容推送到远程库上  
　`$ git push -u origin master`
	>由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。`$ git push origin master`
	
	(3)从远程库克隆  
　`$ git clone url`  
	>Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。

### 分支
- **指令**  
(1)查看分支  
`$ git branch`  
(2)创建分支  
`$ git branch dev`  
(3)切换分支  
`$ git checkout dev`  
(4)创建&切换  
`$ git checkout -b dev`  
(5)合并某分支到当前分支  
`$ git merge dev`  
(6)删除分支  
`$ git branch -d dev`

- **解决冲突**  
先去冲突，然后提交。  
查看分支合并情况：
`$ git log --graph --pretty=oneline --abbrev-commit`

- **分支管理策略**  
　　通常，合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。如果要强制禁用Fast forward模式，Git就会在merge时生成一 个新的commit，这样，从分支历史上就可以看出分支信息。

 　　`$ git merge --no-ff -m "merge with no-ff" dev`

　　查看分支历史  
　　`$ git log --graph --pretty=oneline --abbrev-commit`

- **bug分支**  
应用场景：正在dev分支上开发，在master上出现bug。用`$ git stash`将dev的工作现场保存，切换到master上，新建分支修复bug，并将bug分支merge到master上。随后切回dev分支，恢复现场。  

	(1)查看stash：`$ git stash list`  
	>$ git stash list  
	stash@{0}: WIP on master: 9fe1518 commit the stash files  
	stash@{1}: WIP on master: 9fe1518 commit the stash files  
	stash@{2}: WIP on master: 9fe1518 commit the stash files
	
	
    (2)恢复stash，但不删除：`$ git stash apply`  
	(3)恢复指定stash：`$ git stash apply stash@{2}  

	(4)删除：`$ git stash drop`  
	(5)恢复并删除stash：`$ git stash pop`

- **强行删除分支**  
分支还没有被合并，如果删除，将丢失掉修改
	>`$ git branch -d feature-branch`  
	error: The branch 'feature-vulcan' is not fully merged.
	If you are sure you want to delete it, run 'git branch -D feature-vulcan'.  

	应该用： `$ git branch -D feature-branch`  

### 多人协作
- **正常工作模式**  
(1)首先，可以试图用git push origin branch-name推送自己的修改；  
(2)如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；  
(3)如果合并有冲突，则解决冲突，并在本地提交；  
(4)没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！

　　如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。　　

> 从远程库clone时，默认只能看到master情况，如想看到dev分支，必须创建远程origin的dev分支到本地`$ git checkout -b dev origin/dev`

### 标签管理
- **创建标签**  
`$ git tag v1.0`
- **查看所有标签（按字母顺序）**  
`$ git tag`
- **给指定commitId打标签**  
	查看历史commit：`$ git log --pretty=oneline --abbrev-commit`  
	> 6a5819e merged bug fix 101  
	cc17032 fix bug 101  
	7825a50 merge with no-ff  
	6224937 add merge  
	59bc1cb conflict fixed  
	400b400 & simple  
	75a857c AND simple  
	fec145a branch test  
	d17efd8 remove test.txt  

	打上标签：`$ git tag v1.0 6224937`

- **查看标签信息**  
`$ git show tagName`

- **创建带有说明的标签**  
`$ git tag -a v1.1 -m "commit msg" 7825a50`

- **创建一个用私钥签名标签**  
`$ git tag -s v1.2 -m "signed version 0.2 released" fec145a`
	>签名采用PGP签名，因此，必须首先安装gpg（GnuPG），如果没有找到gpg，或者没有gpg密钥对，就会报错：  
	gpg: signing failed: secret key not available  
	error: gpg failed to sign the data  
	error: unable to sign the tag  
	如果报错，请参考GnuPG帮助文档配置Key。

- **删除标签**  
`$ git tag -d v0.1`

- **推送标签到远程**  
`$ git push origin v1.0`

- **一次性推送全部尚未推送到远程的本地标签**  
`$ git push origin --tags`

- **删除远程标签**  
(1)先从本地删除：`$ git tag -d v1`  
(2)远程删除：`$ git push origin :refs/tags/v1`

### 自定义Git
- **改变颜色**  
`$ git config --global color.ui true`
- **.gitignore**  
[官方配置文件](各种配置文件)  
	>忽略原则：
	忽略操作系统自动生成的文件，比如缩略图等；
	忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
	忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

- **别名**  
`$ git config --global alias.st status`  
`$ git config --global alias.unstage 'reset HEAD'`  
`$ git config --global alias.last 'log -1'`
	>--global针对当前用户，不加代表当前仓库  
	配置文件在.git/config