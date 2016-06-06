---
title: Electron学习
date: 2016-06-05 16:24:16
tags: Electron
categories: 兴趣
---


# 什么是Electron

> If you can build a website, you can build a desktop app. Electron is a framework for creating native applications with web technologies like JavaScript, HTML, and CSS. It takes care of the hard parts so you can focus on the core of your application.

这是[Electron](http://electron.atom.io/)官网上的一段话，从中可以看出它是一款利用Web技术开发跨平台桌面应用的框架，其前身为Atom Shell。Electron的诞生离不开Github开源编辑器[Atom](https://atom.io/)的发布。

最初知道Atom是因为参与了微软[Visual Studio Code文档翻译](https://github.com/jeasonstudio/CN-VScode-Docs)的开源项目，在前期阅读相关资料的时候看到了Atom的一个插件[active-power-mode](https://github.com/JJack27/activate-power-mode)（看了之后觉得，要说会玩，还得是程序员）。之后了解到它使用的Electron技术，可以使用Javascript、HTML、CSS等Web开发语言开发桌面应用。

我一直对Javascript等Web技术很感兴趣，所以就决定在学习Web技术的同时，学习一下Electron的使用。

> 顺便安利一个Mac上很好用的[electronic-wechat](https://github.com/geeeeeeeeek/electronic-wechat)客户端，它就是使用Electron技术开发的开源软件。比万年不更新不修复bug的TX官方客户端好用多了。


# 安装部署Electron开发环境

既然决定学习了，那第一步就是搭建开发环境了。


## 安装Node.js

Electron开发环境依赖于Node.js，所以首先需要安装Node.js。因为使用hexo搭建博客的时候，已经安装过Node.js了，这里不再赘述安装过程。[这里](https://docs.npmjs.com/getting-started/installing-node)有一篇指导文章可以帮助你在不同平台下安装Node.js已经npm包管理器。

Node.js安装完毕之后运行`node -v`命令查看是否安装成功：

![node-v](http://7xnh8y.com1.z0.glb.clouddn.com/node-v.png)

如果安装成功，将会看到node的版本号，如图，我的Node.js版本为v5.3.0。

> 如果你的网络环境比较复杂，无法正常使用npm包管理器（大陆的网络环境通常都比较复杂），可以使用淘宝提供的[npm镜像](http://npm.taobao.org/)，之后将所有`npm`指令都替换为`cnpm`指令即可。


## 安装Electron

安装好node和npm之后就是安装Electron了，它的安装很简单，直接输入：

```shell
npm install electron-prebuilt -g
```

这里的`-g`代表全局安装，如果出现权限问题，可以输入运行：

```shell
sudo npm install electron-prebuilt -g
```

或者去掉`-g`命令试试。



安装完毕后，同样使用`-v`命令检查是否安装成功：

![electron-v](http://7xnh8y.com1.z0.glb.clouddn.com/electron-v.png)


# 运行Electron Quick Start

Electron官方给出了一个简单的示例程序，可以通过其发布在GitHub上的[Repo](https://github.com/atom/electron-quick-start)获取，对于没有安装Git的用户，可以直接点击Download ZIP获取文件，解压缩后，在终端中（Windows的命令行）进入到文件目录，然后运行：

```shell
electron .
```

如果一切顺利，所有相关程序安装完成切正常，就可以看到应用窗口了（注意上面的命令后有一个“.”）。