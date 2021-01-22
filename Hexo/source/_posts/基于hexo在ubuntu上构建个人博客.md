---
title: 基于hexo在ubuntu上构建个人博客
date: 2020-10-19 10:32:25
tags: hexo 
categories:
	- Ubuntu&Github入门学习
---

Github虽有readme提供导读，但终归不是个人博客的形式，因此我在思考如何将markdown语句写成的技术文档轻松地转换成方便阅读的Html格式，并搭建自己的技术金字塔博客。

# 基于hexo在ubuntu上构建个人博客

**hexo** 是一个台湾的大学生写的静态网页框架，好用简单，因此本次选用 **hexo** 搭配 **Github Pages** 完成个人博客网站的制作。

#### 一. 安装 **hexo** 及依赖组件

在使用 **hexo** 前需要确保Github个人账户已完成SSH密钥设定，且已按照之前博客完成了对于git的安装和学习，一切都准备就绪后咱们首先安装 **npm** 及 **Node.js** ：

```bash
sudo apt-get install nodejs
sudo apt install npm
```

接着安装 **hexo**：

```bash
npm install -g hexo-cli
```

我在安装时报错，经查跟 **npm** 源有关, 于是我改用 **cnpm** 进行安装，并且使用管理员权限：

```bash
sudo npm install -g cnpm --registry=https://registry.npm.taobao.org
sudo cnpm install hexo-cli -g
```

#### 二. 将hexo部署至Github





#### 使用picgo搭建图床

部署完网页后，使用 `hexo clean hexo g hexo d `  三连生成远端博客。我发现用.md文件里用在线链接复制的图片都正常显示，而本地路径复制的图片都无法正常显示。经过学习，我发现解决方案都十分的复杂，还需要做很多复制操作。直到图床这个概念进入我的视线。

**图床能干啥？**

官方叫开源图片管理系统，民间理解就是个在网络上存储图片的地方，可以方便在你的博文里调用，加快图片打开速度。常见的图床如下所示：

<img src="https://raw.githubusercontent.com/fanzidanzhidi/andetuchuang/master/typora-blog/20201022174155.png" style="zoom:100%;" />



**PicGo能组撒**？

PicGo一个用于快速上传图片并获取图片 URL 链接的工具。

![img](https://raw.githubusercontent.com/Molunerfinn/test/master/picgo/picgo-2.0.gif)

也就是说他会实现shutter截图-复制到剪贴板-上传到图床-生成markdown支持的链接 一条龙服务。

**Gopic关联Typora实现复制即上传**

在typora偏好设置里更改成如下配置后即可实现复制到typora即同步上传到图床。

![](https://raw.githubusercontent.com/fanzidanzhidi/andetuchuang/master/typora-blog/20201022192222.png)

