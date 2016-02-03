---
layout: post
title: Git 快速入门
category: 工具
tags: Git
keywords: Git，版本控制，教程
description: Git 快速入门
---



### 下载与安装
 - Mac用户：Xcode Command Line Tools自带Git 
 - Linux用户：`sudo yum install git`
 - Windows用户：下载[Git SCM](http://git-for-windows.github.io)

### 检出仓库
执行如下命令以创建一个本地仓库的克隆版本：
`git clone /path/to/repository`
如果是远端服务器上的仓库，你的命令会是这个样子：
`git clone username@host:/path/to/repository` （通过SSH）
或者：
`git clone https:/path/to/repository.git` （通过https）

### 创建新仓库
创建新文件夹，打开，然后执行 `git init`以创建新的 git 仓库。
> 下面每一步中，你都可以通过`git status`来查看你的git仓库状态。

### 工作流
你的本地仓库由 git 维护的三棵“树”组成。第一个是你的 `工作目录`，它持有实际文件；第二个是 `缓存区(Index)`，它像个缓存区域，临时保存你的改动；最后是 `HEAD`，指向你最近一次提交后的结果。
![git工作流程](http://www.bootcss.com/p/git-guide/img/trees.png)

> 事实上，第三个阶段是commit history的图。HEAD一般是指向最新一次commit的引用。现在暂时不必究其细节。

### 添加与提交
你可以计划改动（把它们添加到缓存区），使用如下命令：
```
git add < filename >
git add *
```
这是 git 基本工作流程的第一步。使用如下命令以实际提交改动：
```
git commit -m "代码提交信息"
```
现在，你的改动已经提交到了 HEAD，但是还没到你的远端仓库。

> 在开发时，良好的习惯是根据工作进度及时commit，并务必注意附上有意义的commit message。创建完项目目录后，第一次提交的commit message一般为"Initial commit."。

### 推送改动

你的改动现在已经在本地仓库的 HEAD 中了。执行如下命令以将这些改动提交到远端仓库：
```
git push origin master
```
可以把 master 换成你想要推送的任何分支。 

如果你还没有克隆现有仓库，并欲将你的仓库连接到某个远程服务器，你可以使用如下命令添加：
```
git remote add origin <server>
```
如此你就能够将你的改动推送到所添加的服务器上去了。

>  - 这里origin是< server >的别名，取什么名字都可以，你也可以在push时将< server 替换为origin。但为了以后push方便，我们第一次一般都会先remote add。
>  - 如果你还没有git仓库，可以在Github等代码托管平台上创建一个空(不要自动生成README.md)的repository，然后将代码push到远端仓库。
