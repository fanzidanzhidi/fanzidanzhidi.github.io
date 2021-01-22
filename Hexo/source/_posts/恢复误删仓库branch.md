---
title: # 恢复误操作删掉的的git's Branch
date: 2020-10-19 10:32:25
tags: Git
categories:
	- Ubuntu&Github入门学习
---

这篇文章的背景是基于我在初学git时想要整理我的hexo博客代码并同步合并到Github里。我失误的删除了用作备份的hexo分支，以至于所有的数据、写的md博文全都在本地和远程仓库里消失了。万幸git提供了recover的方式恢复之前删除的branch，那究竟如何操作呢？

# 恢复误操作删掉的的git's Branch

Step1：使用 `git log -g`  语句显示之前commit提交操作的日志记录，并复制你想恢复的那一版本的`commit_id` (commit 后那一长串字符）:

![](https://raw.githubusercontent.com/fanzidanzhidi/andetuchuang/master/typora-blog/20201022142857.png)

Step2：使用 `git branch recover_branch_分支名 commit_id`  创建新分支。

```bash
git branch recover_branch_abc f617c76472e7d6c06703aa82c10a71e996c7aef2
```

这一步将会把你想要恢复的文件全都恢复到新分支

Step3：切换到新分支，检查文件是否存在

```bash
git checkout recover_branch_abc                              
```

可以打开本地仓库检查你想要恢复的代码文档是否已经恢复。

Step0：（花絮）如何删除本地仓库和远程Github仓库的分支呢？

```bash
git branch -D abc                                     #删除本地仓库分支abc
git push origin --delete abc                          #删除远程仓库分支abc
```

:happy: 

:accept:

