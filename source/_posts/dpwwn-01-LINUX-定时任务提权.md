---
title: dpwwn-01 LINUX 定时任务提权
date: 2021-08-19 00:02:32
updated: 2021-08-19 00:02:32
categories: 漏洞复现
password: npfs2333
---





LINUX 定时任务提权实战<!--more-->

# dpwwn-01靶场介绍
在学习了上一篇章（LINUX 定时任务提权）的基础上，进行实战学习，并作如下记录

项目地址：https://www.vulnhub.com/entry/dpwwn-1,342/

靶场介绍：Vulnhub是一个特别好的渗透测试实战网站，提供了许多带有漏洞的渗透测试
渗透要求：靶机需要获得root权限，并找到flag

# 环境配置

因为不知道dpwwn-01靶机的IP，为了方便做题，我们需要将靶机和kali放在同一个局域网里面，这里我选择桥接模式

![图片描述](https://static.cdnjs.cloud/20210808/image-1628388072613_816924564271.png)

注意下靶机和kali的网络适配器都要选桥接
![图片描述](https://static.cdnjs.cloud/20210808/image-1628388123481_640258364027.png)

这里建议连热点来做，不知道为什么连wifi的时候nmap扫不到IP地址

# 信息收集
## 1.存活主机探测
这里利用nmap扫描域内存活主机—–nmap -sP 192.168.43.0/24
![图片描述](https://static.cdnjs.cloud/20210808/image-1628391882462_827766460476.png)


## 2.目标端口收集
上述通过查询得到192.168.43.221 为靶机目标IP
这里利用nmap端口扫描—–nmap -sS 192.168.43.221
![图片描述](https://static.cdnjs.cloud/20210808/image-1628391905524_132035273326.png)

## 3.扫描目录
![图片描述](https://static.cdnjs.cloud/20210808/image-1628392237765_199798186984.png)
看到info.php但是对我们来说没有用

## 4.web页面信息收集

![图片描述](https://static.cdnjs.cloud/20210808/image-1628392210809_540451091790.png)

# 漏洞发现
看到靶机端口有ssh,http,mysql  ，我们尝试Mysql能不能登入

经测试发现mysql为弱口令，密码为空即可成功登入

![图片描述](https://static.cdnjs.cloud/20210808/image-1628391967950_170307116929.png)

连接成功，找到了一个用户可以ssh连接
![图片描述](https://static.cdnjs.cloud/20210808/image-1628394931988_649474389190.png)

ssh连接
```
ssh mistic@192.168.43.221
```

![图片描述](https://static.cdnjs.cloud/20210808/image-1628395148883_296468220290.png)

可以看到不是root权限，需要想办法提权
![图片描述](https://static.cdnjs.cloud/20210808/image-1628395234935_686115852180.png)

使用`find / type f -perm -u=s 2>/dev/null`，查找suid权限的程序
![图片描述](https://static.cdnjs.cloud/20210808/image-1628397096043_348381357485.png)
发现了计划任务crontab。或许我们可以利用此进行提权。

查看一下计划任务
![图片描述](https://static.cdnjs.cloud/20210808/image-1628395430123_461581118501.png)
`cat /etc/crontab`，发现/home/mistic/logrot.sh三分钟执行一次

![图片描述](https://static.cdnjs.cloud/20210808/image-1628395490791_135205471216.png)

看下文件权限，发现这个脚本文件当前账户可写
![图片描述](https://static.cdnjs.cloud/20210808/image-1628395952385_745807070837.png)

# 漏洞攻击

在kali攻击机用msf生成payload（这里IP要写攻击机的，不是靶机ip）
![图片描述](https://static.cdnjs.cloud/20210808/image-1628397824474_396343898328.png)

将反弹shell的代码(即msf生成的payload)写入logrot.sh
> \>和>>的区别，前者是将文件内容覆盖，后者是追加。

![图片描述](https://static.cdnjs.cloud/20210808/image-1628397862850_743452106950.png)

kali开启监听, 开启监听后需要等待一会，因为定时任务三分钟执行一次
![图片描述](https://static.cdnjs.cloud/20210808/image-1628397884521_347882749837.png)
成功提权