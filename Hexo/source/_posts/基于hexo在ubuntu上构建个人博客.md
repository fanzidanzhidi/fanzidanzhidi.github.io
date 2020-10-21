---
title: 基于hexo在ubuntu上构建个人博客
date: 2020-10-19 10:32:25
tags: hexo 
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



