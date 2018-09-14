---
title: Hexo博客多终端管理
date: 2018-09-14 10:49:23
tags: Hexo
categories: Hexo
description: 本文章是个人在优化部署和管理Hexo博客之后做的一个归纳，可以多终端（多台电脑）管理Hexo博客的源代码。
---
# 前言

Hexo博客制作是为了存储我的学习归纳，此前博客的搭建是在公司的电脑上，但是同时我想在家里也更新博客的文章。尝试过直接把github相应仓库的文件git下来，但是git下来的是已经部署好的hexo博客，不好进行修改（除非直接在html文件上修改，但是会很麻烦）。有时候，懒，或者说怕麻烦，也算是促进人进步的一个来源。于是就搜索相关多终端部署的文章，然后自己经历了一些坑之后,完成了这个功能，本文章就是整个流程的整理和总结。

# 步骤

这里在之前Hexo博客搭建好的基础上直接进行多终端管理的流程。

## 创建分支

原有的Github仓库（远程和本地）已经有master，这里再创建一个hexo分支。

在hexo博客的目录下，打开Git Bash。

依次输入命令：`git checkout -b hexo` - `git push origin hexo`

第一个命令为创建并切换本地分支，第二个是将新分支推送到github，也就是创建远程分支。

第一个命令可以拆分使用，变成`git branch hexo`（创建本地分支）和`git checkout hexo`（切换本地分支）。

然后可以使用命令来查看分支:`git branch`（查看本地分支）。还可以使用`git branch -r`查看远程分支，或者使用`git branch -a`查看所有分支。

> 由于hexo分支是以后会更常用的分支，而master只用来放装载后的hexo代码，所以最好在Github上把hexo分支设为默认分支。

## 下载hexo相关

在前步骤的前提下（已经切换到hexo分支），在hexo博客目录下，通过Git Bash执行以下命令：

- `npm install hexo`
- `hexo init`
- `npm install`
- `npm install hexo-deployer-git`

## 修改deploy参数

这个步骤按照之前的创建博客步骤其实是已经完成的了。

修改`_config.yml`中的`deploy`参数，里面的`branch`为`master`。

为了避免不必要的问题，最好确认一下。

## 提交源文件

在hexo分支下，依次运行以下代码：
- `git add .`
- `git commit -m "..."`
- `git push origin hexo`

> 这里可以检查是否成功，打开Github的相应仓库，查看hexo分支，可以找到放置文章的文件夹，进去确认一下自己的文章即可：`../source/_posts`。

## hexo博客的装载和部署

执行:`hexo generate -d`就可以部署到网站上了。

> 这里我之前还在疑惑会不会把装载的文件更新到了hexo分支上了，但是其实我们不需要担心这一点，因为我们一的`_config.yml`文件中的`deploy`参数中，分支是选择`master`的。网页的装载后的代码只会更新到`master`，`hexo`只会更新前面提交的源文件。

## 后续的日常更新

日常更新博客，在修改之后，按照上面的`提交源文件`和`hexo博客的装载和部署`的步骤进行即可。

> 原教程上面说：虽然这两个步骤调换顺序一般不会有问题，但是个别情况下可能还是会有问题，所以为了避免不必要的麻烦，尽量使用这个顺序来进行操作。

## 本地资料丢失 & 增加新的管理终端

在确保ssh和必须的东西都齐全的情况下，进行第一步。

首先就是拷贝仓库了。

使用Git命令拷贝仓库的hexo分支。

然后在本地仓库目录下通过Git Bash执行以下代码：

- `npm install hexo`
- `npm install`
- `npm install hexo-deployer-git`

需要记住的是，这里不需要`hexo init`这条命令了，并不是我打少了。

# 总结

多终端管理可以方便我们在多个地方进行博客管理，同时又不比为同步的事情过多烦恼。虽然通过百度云等网盘网站也可以进行类似操作，不过其缺点是无法同步版本。所以无论是方便程度还是优缺点而言，使用Github的分支实现一个多终端管理是最方便的。