---
title: Github同步文件时子仓库无法打开的解决方案
date: 2020-10-19 16:32:25
tags: Github
---

本文主要介绍在使用hexo+Ubuntu18.04LTS+github pages 制作个人blog方案时遇到的子文件夹无法打开的解决方案。

## Github同步文件时子仓库无法打开的解决方案

### 报错背景

在使用**hexo**制作博客时尝尝需要使用别人自制的模版，于是乎我们要跑到人家的仓库里去clone或者fork到自己的博客仓库里，等我们兴高采烈的做完自己博客，想要同步 **git add** 到自己的仓库时，却发现themes里的那个主题文件夹上面有个小箭头，并且我们在github上打不开那个文件夹。

![](https://raw.githubusercontent.com/fanzidanzhidi/andetuchuang/master/typora-blog/20201021193930.png)

![](https://raw.githubusercontent.com/fanzidanzhidi/andetuchuang/master/typora-blog/20201021193956.png)



### 报错原因

> 在 GitHub 上看到的将是一个灰色的图标，代表这是一个子模块，但是不知道这个子模块的仓库所在的 url，因此在 GitHub 上无法打开这个文件夹。

### 解决方案

Step1: cd到子文件夹所在目录，删除已经staged的文件，文件夹为无法打开的子仓库

```bash
git rm -r --cached "文件夹的名称" 
```

Step2: 重新stage这个文件夹

```bash
git add themes/文件夹的名称/
```

注意： 最后一定要加 `/` ，表示加入这个文件夹而非作为子模块

Step3：重新git add、commit、push仓库至github