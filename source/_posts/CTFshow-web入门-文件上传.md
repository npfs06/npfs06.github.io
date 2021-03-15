---
title: CTFshow_web入门_文件上传
date: 2021-03-08 08:46:00
updated: 2021-03-08 08:46:00
categories: web入门
---

CTFshow_web入门_文件上传<!--more-->

## web151

fuzz之后，发现可以上传png格式的图片，文件上传，burp suite抓包，改后缀为php，蚁剑连接即可

![](https://img.npfs06.top/20210308090026.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## web152



同web151



## web153

本题考察利用上传user.ini进行文件上传绕过。对于user.ini直接献上官方文档。

```
自 PHP 5.3.0 起，PHP 支持基于每个目录的 INI 文件配置。此类文件 仅被 CGI／FastCGI SAPI 处理。此功能使得 PECL 的 htscanner 扩展作废。如果你的 PHP 以模块化运行在 Apache 里，则用 .htaccess 文件有同样效果。

除了主 php.ini 之外，PHP 还会在每个目录下扫描 INI 文件，从被执行的 PHP 文件所在目录开始一直上升到 web 根目录（$_SERVER['DOCUMENT_ROOT'] 所指定的）。如果被执行的 PHP 文件在 web 根目录之外，则只扫描该目录。

在 .user.ini 风格的 INI 文件中只有具有 PHP_INI_PERDIR 和 PHP_INI_USER 模式的 INI 设置可被识别。
```

也就是说如果你目录下有user.ini会先去识别里面的配置。当然文档也说了，并不是所有的配置都可以识别。只有 PHP_INI_PERDIR 和 PHP_INI_USER 模式可以。

![](https://img.npfs06.top/20210308092243.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

那么我们就去找我们需要用到配置
发现auto_append_file和auto_prepend_file
一个相当于在每个php文件尾加上 include(“xxxx”)，一个相当于文件头加上 include(“xxx”)
其中xxx就是 auto_append_file的值。

**为了利用auto_append_file，我们首先上传.user.ini内容为 auto_append_file=“xxx” xxx为我们上传的文件名，接着上传一个带木马的图片 , 这样就在每个php文件上包含了我们的木马文件。**

![](https://img.npfs06.top/20210308093905.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](https://img.npfs06.top/20210308093827.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

这里要注意一下，蚁剑连接的url为upload目录

![](https://img.npfs06.top/20210308093515.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

