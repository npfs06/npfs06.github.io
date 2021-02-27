---
title: Hexo如何删除git commit历史记录
date: 2021-02-26 20:15:41
categories: problems
---

有时博客发布之后，会对博客内容再进行修改，如果不想要别人看到 Hexo 博客的修<!--more-->改历史，只保留最后的版本，那我们可以把以前的 git commit 历史记录清理掉，这样还可以减少占用的 Pages 空间。操作步骤如下：



> 删除博客根目录下的 .deploy_git 文件夹。为防意外删前最好备份一下，git commit 历史记录就保存在这个文件夹

在 Git Bash 或终端中执行命令：



```
hexo clean
```

在 Git Bash 或终端中执行命令：

```
hexo d -g
```