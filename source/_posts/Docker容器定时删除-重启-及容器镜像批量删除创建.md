---
title: Docker容器定时删除(重启)及容器镜像批量删除创建
date: 2021-01-01 15:35:04
updated: 2021-01-01 15:35:04
categories: Docker
---

关于Docker容器的定时删除（重启）及容器镜像的批量删除和批量创建<!--more-->

```
镜像创建：
docker build -f Dockerfile -t [随意命名] .
容器创建:
docker run -itd -p 12345:80 --nmae [随意命名] [镜像名]
```



# 直接删除所有镜像或容器

```bash
# 直接删除所有镜像
docker rmi `docker images -q`

# 直接删除所有容器
docker rm `docker ps -aq`
```

# 按条件筛选之后删除

```bash
# 按条件筛选之后删除镜像
docker rmi `docker images | grep xxxxx | awk '{print $3}'`

# 按条件筛选之后删除容器
docker rm `docker ps -a | grep xxxxx | awk '{print $1}'`
```



# Docker容器的定时删除重建（重启）

```python
# python脚本实现
import os
os.system('docker rm -f sqli-labs')   //删除容器
os.system('docker run -itd -p 12345:80 --name sqli-labs [镜像id]')  //新建容器
```

然后利用crontab设定时任务，定时执行py脚本

**ubuntu安装cron**

```
安装：apt-get install cron
启动：service cron start
重启：service cron restart
停止：service cron stop
检查状态：service cron status
查询cron可用的命令：service cron
检查Cronta工具是否安装：crontab -l
```
第一次进入的时候要选择编辑器，选择后你可以配置新cron作业了，一般是选3

> crontab -e   //vim编辑器

![](https://img.npfs06.top/20210404154527.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

图中命令的意思是每天23:59 执行/root/dingshi.py脚本（脚本内容就是上文的python内容）

```
//crontab 时间设置

*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12) 
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```

