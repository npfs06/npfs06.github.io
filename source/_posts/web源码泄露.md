---
title: web源码泄露
date: 2020-04-21 23:50:16
updated: 2020-04-21 23:50:16
categories: web
---

web中常见的源码泄露<!--more-->

# vim临时文件

```
  程序员使用vim编辑器编写一个`index.php`文件时，会有一个`.index.php.swp`文件，如果文件正常退出，则该文件被删除，如果异常退出，该文件则会保存下来，该文件可以用来恢复异常退出的index.php，同时多次意外退出并不会覆盖旧的`.swp`文件，而是会生成一个新的，例如`.swo`文件。
```

## 恢复文件

> vim -r  恢复的文件名
>
> >  前提是.index.php.swp存在于该目录下





# vim备份文件

默认情况下使用Vim编程，在修改文件后系统会自动生成一个带`~`的备份文件，某些情况下可以对其下载进行查看；

> 以 index.php 为例 第一次产生的缓存文件名为 .index.php.swp
> 第二次意外退出后，文件名为.index.php.swo
> 第三次产生的缓存文件则为 .index.php.swn
> 注意：index前有 " . "





# PHPSTORM

使用phpstorm新建项目时，会生成一个`.idea`文件夹，这个文件夹有一个`workspace.xml`，访问可以得到一些信息。





# 网站备份压缩文件

在网站的使用过程中，往往需要对网站中的文件进行修改、升级。此时就需要对网站整站或者其中某一页面进行备份。当备份文件或者修改过程中的缓存文件因为各种原因而被留在网站web目录下，而该目录又没有设置访问权限时，便有可能导致备份文件或者编辑器的缓存文件被下载，导致敏感信息泄露，给服务器的安全埋下隐患。



## 漏洞成因及危害

该漏洞的成因主要有以下两种：

> 服务器管理员错误地将网站或者网页的备份文件放置到服务器web目录下。
> 编辑器在使用过程中自动保存的备份文件或者临时文件因为各种原因没有被删除而保存在web目录下。

## 漏洞检测

该漏洞往往会导致服务器整站源代码或者部分页面的源代码被下载，利用。源代码中所包含的各类敏感信息，如服务器数据库连接信息，服务器配置信息等会因此而泄露，造成巨大的损失。被泄露的源代码还可能会被用于代码审计，进一步利用而对整个系统的安全埋下隐患。



## 常见备份压缩文件

> .rar
> .zip
> .7z
> .tar.gz
> .bak
> .swp
> .txt
> .html

## 使用disearch扫描

> python dirsearch.py -u   url  -e *





# git源码泄漏

git在初始化项目的时候, 会在项目的根目录(可用`git rev-parse --show-toplevel`查看)创建一个名为`.git`的隐藏文件夹, 里面包含了本地所有commit的历史记录. 如果无意间将这个目录置于WEB的路径下让用户可以访问,那么这也就泄露了几乎所有的源代码和其他其他敏感信息.

该文件可以来恢复源代码

## fuzz：

```
http://url/.git/config
```

若存在该页面，可以看到配置信息，说明存在该漏洞

## 漏洞利用：

工具：[GitHack](https://github.com/lijiejie/GitHack)

```
python GitHack.py http://url/.git/
```

> git log可以查看提交历史
> git diff查看git提交的不同处
> stash命令可用于临时保存和恢复修改，可跨分支
> git stash list查看所有保存的记录列表
> git stash pop，从 git 栈中弹出来一个文件

工具：[dvcs-ripper](https://github.com/kost/dvcs-ripper)

```
rip-hg.pl -v -u http://url/.git
```



# hg泄露

与git类似, hg在初始化项目时, 会在项目的根目录下创建一个名为`.hg`的隐藏文件夹,里面包含了代码和分支的修改记录和开发人员的相关信息. 当开发人员使用 Mercurial 进行版本控制，对站点自动部署。如果配置不当,可能会将.hg 文件夹直接部署到线上环境。这就引起了 hg 泄露漏洞。

`http://www.example.com/.hg/`

## fuzz：

```
http://url/.hg/
```

## 漏洞利用：

工具：[dvcs-ripper](https://github.com/kost/dvcs-ripper)

```
rip-hg.pl -v -u http://url/.hg
```



# SVN泄露

svn同样在项目根目录下会创建一个名为`.svn`的隐藏文件夹, 包含了所有分支commit信息和代码记录.

## fuzz：

```
http://url/.svn/entries
```

## 漏洞利用：

工具：[dvcs-ripper](https://github.com/kost/dvcs-ripper)

```
rip-svn.pl -v -u http://www.example.com/.svn/
```



# bzr/Bazaar泄露

bzr在初始化项目时(bzr init/init-repo), 会在项目根目录产生名为`.bzr`的隐藏目录, 同样暴露了源代码和用户信息.

## 漏洞利用：

工具：[dvcs-ripper](https://github.com/kost/dvcs-ripper)

```
rip-bzr.pl -v -u http://www.example.com/.bzr/
```



# CVS泄漏

cvs项目在初始化(cvs checkout `project`)的时候, 会在`project`目录下创建一个名为`CVS`的目录,
其中保存了各个文件的修改和commit记录. 通过此目录可以获取代码的历史版本. 其中两个关键文件为:
`CVS/Root`和`CVS/Entries`, 分别记录了项目的根信息和所有文件的结构

## fuzz

```
http://url/CVS/Root 返回根信息
http://url/CVS/Entries 返回所有文件的结构
```

## 漏洞利用

取回源码的命令，这个命令的意思就是把远端一个名为name的repo clone到本地名为dir的目录下。

```
bk clone http://url/name dir
```

查看所有的改变的命令，转到download的目录

```
bk changes
```

# .DS_Store文件泄漏

.DS_Store是Mac下Finder用来保存如何展示 文件/文件夹 的数据文件，每个文件夹下对应一个。如果开发/设计人员在发布代码时未删除文件夹中隐藏的.DS_store，则有可能会造成文件泄露

## fuzz：

```
http://url/.DS_Store
```

## 漏洞利用：

工具：[dsstoreexp](https://github.com/lijiejie/ds_store_exp)

```
python ds_store_exp.py http://url/.DS_Store
```



# WEB-INF/web.xml泄露

WEB-INF是Java的WEB应用的安全目录。如果想在页面中直接访问其中的文件，必须通过web.xml文件对要访问的文件进行相应映射才能访问。

通常一些web应用我们会使用多个web服务器搭配使用，解决其中的一个web服务器的性能缺陷以及做均衡负载的优点和完成一些分层结构的安全策略等。在使用这种架构的时候，由于对静态资源的目录或文件的映射配置不当，可能会引发一些的安全问题，导致web.xml等文件能够被读取。

WEB-INF主要包含一下文件或目录：

- `/WEB-INF/web.xml`：Web应用程序配置文件，描述了 servlet 和其他的应用组件配置及命名规则。
- `/WEB-INF/classes/`：含了站点所有用的 class 文件，包括 servlet class 和非servlet class，他们不能包含在 .jar文件中
- `/WEB-INF/lib/`：存放web应用需要的各种JAR文件，放置仅在这个应用中要求使用的jar文件,如数据库驱动jar文件
- `/WEB-INF/src/`：源码目录，按照包名结构放置各个java文件。
- `/WEB-INF/database.properties`：数据库配置文件漏洞检测以及利用方法

## 漏洞检测以及利用方法

通过找到web.xml文件，推断class文件的路径，最后直接class文件，在通过反编译class文件，得到网站源码。

```
http://url/WEB-INF/web.xml
```

# reference

<a href="https://0clickjacking0.github.io/2019/04/17/web%E5%B8%B8%E8%A7%81%E6%BA%90%E7%A0%81%E6%B3%84%E9%9C%B2/" target="_blank">https://0clickjacking0.github.io/2019/04/17/web常见源码泄露/</a>
<a href="https://www.secpulse.com/archives/55286.html" target="_blank">https://www.secpulse.com/archives/55286.html</a>
<a href="https://www.cnblogs.com/pannengzhi/p/2017-09-23-web-file-disclosure.html" target="_blank">WEB中的敏感文件泄漏</a>