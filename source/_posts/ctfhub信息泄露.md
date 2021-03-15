---
title: ctfhub信息泄露
date: 2020-04-23 09:33:51
updated: 2020-04-23 09:33:51
categories: Write up
---

可用扫描工具尝试扫描（disearch、御剑等)<!--more-->

# 目录遍历

**Method One**

直接手动遍历

**Method Two**

```
import requests
url = "url"
for i in range(5):
    for j in range(5):
        a =url+"/"+str(i)+"/"+str(j)
        r = requests.get(a)
        r.encoding = 'utf-8'
        get_file=r.text
        if "flag.txt" in get_file:
            print(a)
```



# PHPINFO

`Ctrl+F`搜索flag

要注意下ctfhub后面有个空格，要删掉





# 备份文件下载



## 网站源码

拿disearch扫一下

```
python dirsearch.py -u http://challenge-d7b90efa6684f0b0.sandbox.ctfhub.com:10080/ -e *
```

可以发现www.zip，访问下载

可以发现里面有三个文件

![](https://s1.ax1x.com/2020/04/22/JYjos1.png)

urlf访问`.txt`,得到flag



## bak文件

题目提示Flag in index.php source code

访问`index.php.bak`下载得到源码，flag在源码里



## vim缓存

      程序员使用vim编辑器编写一个`index.php`文件时，会有一个`.index.php.swp`文件，如果文件正常退出，则该文件被删除，如果异常退出，该文件则会保存下来

访问`.index.php.swp`,下载文件，记事本打开，`Ctrl+F`搜索`ctfhub`得到flag



## .DS_Store

.DS_Store是Mac下Finder用来保存如何展示 文件/文件夹 的数据文件，每个文件夹下对应一个。如果开发/设计人员在发布代码时未删除文件夹中隐藏的.DS_store，则有可能会造成文件泄露

访问`http://url/.DS_Store`,下载文件

打开后发现乱码，使用.DS_Store 的解析工具Python-dsstore进行文件的解析。

等到的文件url访问，等到flag