---
title: 使用Hexo+GitHub io创建个人博客
tags: Hexo
categories: Hexo
description: 记录一下自己创建博客的过程，和一些写文章时候的语法注意事项。
---
## 创建库

在自己的GitHub账号上创建一个新的库，库的名称格式为"userName.GitHub.io"，这里的userName必须是你的GitHub账号名称。

同时记得勾上Initialize this repository with a README。

然后创建。

> 为了不必要的麻烦，不要使用其他的名字格式来创建io。
> 每个账号最多有一个io。

## 查看GitHub Pages

在创建好的库右上方有一个settings，点击它并且下拉到GitHub Pages

这里会发现有一个绿色框，里面有一个网址，并且这个网址和你创建的库的名称一样，点开后会发现你创建的库被部署到网络上了。

## 配置SSH Key（可选）
### 设置原因
提交代码是需要你的GitHub权限的，如果没有配置SSH Key是需要直接使用用户名和密码。如果觉得直接使用用户名和密码不安全，可以配置SSH Key来用于连接本地和服务器。
### 步骤

打开Git bash，输入以下代码查看是否已经存在SSH Keys：

`cd ~/.ssh`

如果存在，可以删除.ssh文件夹里面所有文件，或者等下直接覆盖。然后输入


    ``` 
	ssh-keygen -t rsa -C "邮箱地址"
	``` 

回车三次，在用户目录下找到一个.ssh\id_rsa.pub的文件，用记事本打开并复制内容。 （这里直接回车三次，使用了默认的值，如果想要详细设置，输入完成之后根据自己情况来设置内容再回车。）

打开你的GitHub主页，进入个人设置 -> SSH and GPG keys -> New SSH Key
然后自己增加一个标题，并且把上面复制的内容粘贴到Key下的输入框内，点添加就完成了。


### 检测是否成功

输入

	``` 
	ssh -T git@github.com 
	```

如果提示Are you sure you want to continue connecting (yes/no)?  输入yes
如果跳出类似以下信息则说明配置成功：Hi XXX! You've successfully authenticated, but GitHub does not provide shell access.


## 使用Hexo制作个人博客

### 安装hexo

在命令行或者Git Bash 输入以下代码进行hexo的全局安装

    ``` 
	npm install -g hexo 
	```

### 初始化Hexo

创建一个文件夹，并且在该文件夹内，右键运行Git Bash，输入命令：
   
    ``` 
	hexo init 
	```

当出现提示

> INFO Start blogging with Hexo！

便可以了，可以发现文件夹内出现了几个文件。

使用编程软件打开该文件夹，之后打开_config.yml文件，配置基础信息。这里用一张教程里面的图片：

![](https://upload-images.jianshu.io/upload_images/1531909-cd5743eda172deca.png?imageMogr2/auto-orient/)

>我的theme在差不多最下面的位置，也就是说位置可能发生变化，自己查找一下便可。主题是博客的整个布局样式主题，主题可以下载。

### 本地浏览hexo

在文件夹下打开Git Bash，输入以下代码：

    ``` 
	hexo g && hexo s 
	```

之后输入http://localhost:4000/即可本地查看博客样式。

![](https://upload-images.jianshu.io/upload_images/1531909-4f9a111a4f87ff63.png?imageMogr2/auto-orient/)

> 这里有可能会出现浏览器一直在转圈但是加载不出来的问题，一般情况下是端口占用问题。我没有出现这个问题，若有出现端口冲突问题，自行百度解决即可。

### 更新到github.io

打开hexo博客源文件中的`_config.yml`文件，修改deploy参数：
	
	```
	deploy: 
	  type: git
	  repository: 这里填写你仓库的SSH地址。不能使用HTTPS地址。
	  branch: master

	```

进行部署，在源文件下使用Git Bash执行以下代码：
- `hexo g`
- `hexo d`

### 使用hexo写文章

在该文件夹的路径下找到 ..\source\_posts文件夹，打开之后在里面新建.md文件即可写文章。

> 每次写完文章之后都记住要执行`hexo g`和`hexo d`重新部署一下博客，否则内容不会更新。

#### 关于语法

Hexo中对并不是对所有MarkDown语法都可以完全识别。针对这个情况，在经过自己常用的语法中，亲自测试之后，做了一下部分统计。

- 标题

标题#符号和标题名字之间必须空出空格，否则无法识别。

- 代码

单行代码可以使用反引号``语法，多段代码可以使用三对嵌套的反引号来包含起来即可，同时代码块的反引号要独立一行，否则会造成开头引用的import无法显示（其他某些代码是否也不会显示个人未测试，不过能避免就直接全避免了吧）。

- 引用

目前还没找到更好地方法，`>`和引用内容中间空一行也会出现变成的`“`情况。不知是否和所使用的主题有关。

- Hexo的东西

	```
	title: 文章标题
	tags: 标签
	date: 建立日期 - 可以不设置
	updated: 更新日期
	categories: 分类
	description: 简介
	--- 分隔符
	```
注意冒号之后一定要有个空格。