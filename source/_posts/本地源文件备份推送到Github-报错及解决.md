---
title: 本地源文件备份推送到Github 报错及解决
date: 2021-02-24 10:55:59
categories: problems
---

在我企图在`github`的xxx.github.io仓库新建分支对基于hexo搭建的blog通过hexo-git-backup插件进<!--more-->行备份时出现了以下的问题

## 问题出现

根据网上教程，我查看了本地hexo版本

```
hexo version
```

![](http://img.npfs06.top/20210224110039.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

因此我使用了这条命令：

```
npm install hexo-git-backup --save
```

进行博客备份插件安装

同时我在github仓库建立新分支，我创建的分支名为backup



然后我在本地博客根目录的配置文件`_config.yml`最下方加入以下内容

```
# backup
backup:
  type: git
  theme: next-reloaded
  message: Back up my www.npfs06.top blog
  repository:
    github: https://github.com/xxxx/xxxx.github.io.git,backup
```

>**theme** 是主题名
**message** 是自定义信息
**branchName** 是分支名，用英文逗号和仓库名隔开备份



但是在我进行最后一步 `hexo b`的时候，问题出现了

![](http://img.npfs06.top/20210224110506.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





## 解决方案



我在`node_modules/hexo-git-backup/git.js` 文件中加入一行以显示执行的命令 ~~（行77）~~

![](http://img.npfs06.top/20210224112511.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



再次运行`hexo b`得到报错信息如下

![](http://img.npfs06.top/20210224112612.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



以此可见错误原因。

为解决此问题，我做了如下的改动

我在`node_modules/hexo-git-backup/git.js` 文件中，将这一行~~（行135）~~

> commands.push(['push', '-u', t, 'master:' + repo[t].branch, '--force']);
> 改成
> commands.push(['push', '-u', t, 'backup:' + repo[t].branch, '--force']);

![](http://img.npfs06.top/20210224112539.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



然后在博客根目录配置文件`_config.yml`中将`github` 更改为`origin`

```
# backup
backup:
  type: git
  theme: next-reloaded
  message: Back up my www.npfs06.top blog
  repository:
    origin: https://github.com/xxxx/xxxx.github.io.git,backup
```

重新运行`hexo b`

![](http://img.npfs06.top/20210224113023.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

问题解决！