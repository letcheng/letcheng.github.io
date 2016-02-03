---
layout: post
title: Git 添加和提交到本地仓库
category: 工具
tags: Git
keywords: Git
description:  Git 添加和提交到本地仓库
---



### 添加到缓存区

`git add`命令将工作目录中的变化添加到缓存区。它告诉Git你想要在下一次提交时包含这个文件的更新。但是，`git add`不会怎么影响你的仓库——在你运行`git commit`前更改都不会被记录。

使用这些命令之时，你还需要`git status`来查看工作目录和缓存区的状态。

#### 用法
``` 
git add <file>
```
将`<file>`中的更改加入下次提交的缓存。
``` 
git add <directory>
```
将`<directory>`下的更改加入下次提交的缓存。
``` 
git add -p
```
开始交互式的缓存，你可以选择文件的一部分加入到下次提交缓存。它会向你展示一堆更改，等待你输入一个命令。`y`将这块更改加入缓存，`n`忽略这块更改，`s`将它分割成更小的块，`e`手动编辑这块更改，以及`q`退出。

#### 最佳实践与例子

`git add`和`git commit`这两个命令组成了最基本的Git工作流。每一个Git用户都需要理解这两个命令，不管他们团队的协作模型是如何的。我有一千种方式可以将项目版本记录在仓库的历史中。

在一个只有编辑、缓存、提交这样基本流程的项目上开发。首先，你要在工作目录中编辑你的文件。当你准备备份项目的当前状态时，你通过`git add`来缓存更改。当你对缓存的快照满意之后，你通过`git commit`将它提交到你的项目历史中去。

![Git Tutorial: git add Snapshot](https://www.atlassian.com/git/images/tutorials/getting-started/saving-changes/01.svg)

`git add`命令不能和 `svn add`混在一起理解，后者将文件添加到仓库中。而`git add` 发生于更抽象的 *更改* 层面。也就是说，`git add`在每次你修改一个文件时都需要被调用，而`svn add`只需要每个文件调用一次。这听上去很多余，但这样的工作流使得一个项目更容易组织。

当你开始新项目的时候，`git add`和`svn import`类似。为了创建当前目录的初始提交，使用下面两个命令：
``` 
git add .
git commit
```
当你项目设置好之后，新的文件可以通过路径传递给`git add`来添加：
``` 
git add hello.py
git commit
```
上面的命令同样可以用于记录已有文件的更改。重复一次，Git不会区分缓存的更改来自新文件，还是仓库中已有的文件。


#### 扩展：缓存区

缓存区是Git更为独特的地方之一，如果你是从SVN（甚至是Mercurial）迁移而来，那你可得花点时间理解了。你可以简单地把它想成是工作目录和项目历史之间的缓冲区。

缓存允许你在实际提交到项目历史之前，将相关的更改组合成一份高度专注的**快照**，而不是将你上次提交以后产生的所有更改一并提交。也就是说你可以更改各种不相关的文件，然后回过去将它们按逻辑切分，将相关的更改添加到缓存，一份一份提交。在任何修改控制系统中，很重要的一点是提交必须是原子性的，以便于追踪bug，并用最小的代价回滚更改。


### 提交到仓库

`git commit`命令**将缓存的快照提交到项目历史**。提交的快照可以认为是项目『安全』的版本——Git永远不会改变它们，除非你这么要求。和`git add`一样，这是最重要的Git命令之一。

尽管和它和`svn commit`名字一样，但实际上它们毫无关联。快照被提交到**本地仓库**，不会和其他Git仓库有任何交互。

#### 用法
``` 
git commit
```
提交已经缓存的快照。它会运行文本编辑器，等待你输入提交信息。当你输入信息之后，保存文件，关闭编辑器，创建实际的提交。
``` 
git commit -m "<message>"
```
提交已经缓存的快照。但将`<message>`作为提交信息，而不是运行文本编辑器。
``` 
git commit -a
```
提交一份包含工作目录所有更改的快照。它只包含跟踪过的文件的更改（那些之前已经通过`git add`添加过的文件）。

#### 最佳实践与例子

快照总是提交到 *本地*仓库。这一点和SVN截然不同，后者的工作拷贝提交到中央仓库。而Git不会强制你和中央仓库进行交互，直到你准备好了。就像缓存区是工作目录和项目历史之间的缓冲地带，每个开发者的本地仓库是他们贡献的代码和中央仓库之间的缓冲地带。

这一点改变了Git用户基本的开发模型。Git开发者可以在本地仓库中积累一些提交，而不是一发生更改就直接提交到中央仓库。这对于SVN风格的协作有着诸多优点：更容易将功能切分成原子性的提交，让相关的提交组合在一起，发布到中央仓库之前整理好本地的历史。开发者得以在一个隔离的环境中工作，直到他们方便的时候再整合代码。

SVN和Git除了使用上存在巨大差异，它们底层的实现同样遵循截然不同的设计哲学。SVN追踪文件的 *变化* ，而Git的版本控制模型基于 *快照* 。比如说，一个SVN提交由仓库中原文件相比的差异(diff)组成。而Git在每次提交中记录文件的 *完整内容* 。

![Git Tutorial: Snapshots, Not Differences](https://www.atlassian.com/git/images/tutorials/getting-started/saving-changes/02.svg)

这让很多Git操作比SVN来的快得多，因为文件的某个版本不需要通过版本间的差异组装得到——每个文件完整的修改能立刻从Git 的内部数据库中得到。

Git的快照模型对它版本控制模型的方方面面都有着深远的影响，从分支到合并工具，再到协作工作流，以至于影响了所有特性。

下面这个栗子假设你编辑了`hello.py`文件的一些内容，并且准备好将它提交到项目历史。首先，你需要用`git add`缓存文件，然后提交缓存的快照。

``` 
git add hello.py
git commit
```

它会打开一个文件编辑器（可以通过`git config`设置)询问提交信息，同时列出将被提交的文件。

``` 
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
#modified: hello.py
```

Git对提交信息没有特定的格式限制，但约定俗成的格式是：在第一行用50个以内的字符总结这个提交，留一空行，然后详细阐述具体的更改。比如：

``` 
Change the message displayed by hello.py

- Update the sayHello() function to output the user's name
- Change the sayGoodbye() function to a friendlier message
```

注意，很多开发者倾向于在提交信息中使用一般现在时态。这样看起来更像是对仓库进行的操作，让很多改写历史的操作更加符合直觉。