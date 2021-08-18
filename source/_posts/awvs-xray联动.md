---
title: awvs+xray联动
date: 2021-08-16 16:43:44
updated: 2021-08-16 16:43:44
categories: 工具
---

awvs 的爬虫很好用，支持表单分析和单页应用的爬取，xray 的扫描能力比较强，速度也更快。awvs 和 xray 搭配使用则是如虎添翼。这里演示的是扫描 awvs 的在线靶站 http://testphp.vulnweb.com/

<!--more-->



# 环境

一台云服务器



# awvs

awvs我是用docker搭的，具体参见

<a href="https://npfs06.top/2021/08/16/awvs14%E7%A0%B4%E8%A7%A3%E7%89%88docker%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85/" target="_blank">awvs14破解版docker一键安装</a>

# xray



下载链接：https://github.com/chaitin/xray/releases/download/1.7.1/xray_linux_amd64.zip



下好之后，可能会运行不了，这是因为没给权限

> chmod +x xray_linux_amd64

<img src="http://img.npfs06.top/20210816164905.png" style="zoom:80%;" />







# awvs+xray

看下ip地址

> ifconfig -a



<img src="http://img.npfs06.top/20210816165042.png" style="zoom:80%;" />





awvs 选好目标后，配置http代理

<img src="http://img.npfs06.top/20210816165153.png" style="zoom:80%;" />





保存好，选crawl only 就可以开始扫描了

<img src="http://img.npfs06.top/20210816165243.png" style="zoom:80%;" />



同时 xray要开启监听

> ./xray_linux_amd64 webscan --listen 0.0.0.0:32701 --html-output awvs.html

# 结果





<img src="http://img.npfs06.top/20210816165413.png" style="zoom:80%;" />





扫描结束会生成如下文件



<img src="http://img.npfs06.top/20210816165515.png" style="zoom:80%;" />



<img src="http://img.npfs06.top/20210816165553.png" style="zoom:80%;" />



