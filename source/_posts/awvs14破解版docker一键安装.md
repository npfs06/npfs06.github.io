---
title: awvs14破解版docker一键安装
date: 2021-08-16 16:04:30
updated: 2021-08-16 16:04:30
categories: 工具
---



前段时间云服务器过期，很多东西都要迁移，刚好这几天漏洞盒子在冲月榜，重新搭了下awvs，结果发现死活访问不了网站，这里做个记录 <!--more-->



# 1.使用docker查看是否有awvs；

```
root@111:~# docker search awvs
NAME                       DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
secfa/docker-awvs          AWVS14 210503151  Linux Version Crack By Fah…   52                   
leishianquan/awvs-nessus                                                   21                   
xrsec/awvs                 Awvs 14 Scanner、fahai                           4                    
tiancizzz/awvs             一款知名的网络漏洞扫描工具                                   1                    
lazj/awvs13                                                                1                    
k3rwin/awvs13                                                              0                    
hangezhao/awvs                                                             0                    
xxlm/awvs13cn                                                              0                    
zeyanlin/awvs                                                              0                    
xfantasy/awvs                                                              0                    
smarttang/awvs_2019_07                                                     0                    
110110/awvs13                                                              0                    
jstang/awvs                配置了API密钥的容器镜像                                   0                    
meterpreter/awvs                                                           0                    
littlehoury/awvs           A wonderful virtual sleep                       0                    
evil0x0/awvs                                                               0                    
saline/awvsengine                                                          0                    
sigle0724/awvs                                                             0                    
yimingy72/awvs14                                                           0                    
nienie/awvs13                                                              0                    
cure0jvs/awvs-engine                                                       0                    
xschur666/awvs                                                             0                    
ganl/awvs                                                                  0                    
yakoazz/awvsnessus                                                         0                    
srsecmmm/awvs13            分布式部署                                           0                                        
```

# 2.这里我们下载第一个下载次数最多的；

```
[root@111]# docker pull secfa/docker-awvs
Using default tag: latest
latest: Pulling from secfa/docker-awvs
da7391352a9b: Pull complete 
14428a6d4bcd: Pull complete 
2c2d948710f2: Pull complete 
92d53db52715: Pull complete 
dddab6abad69: Pull complete 
Digest: sha256:f10492ebd89a900ac0bba810690b621a839fefc1026bfc3a99ae54ac423742a0
Status: Downloaded newer image for secfa/docker-awvs:latest
```

# 3.查看安装的镜像

```
[root@111]# docker images
REPOSITORY                       TAG                 IMAGE ID            CREATED             SIZE
secfa/docker-awvs                latest              5c1a22cc10bd        2 months ago        1.04GB
pxc                              latest              ad45cffb713a        8 months ago        491MB
percona/percona-xtradb-cluster   5.7                 ad45cffb713a        8 months ago        491MB
```

# 4.启动停止

```
# 起别名的方式启动
docker run -d -p 3443:3443 --name my-awvs secfa/docker-awvs
以后停止启动的时候使用别名启动停止：
[root@localhost ~]# docker stop my-awvs
 
[root@localhost ~]# docker start my-awvs
 
 
docker run -d -p 3443:3443 secfa/docker-awvs
 
[root@hadoop-03 ~]# docker stop 5c5eca394479
5c5eca394479
[root@hadoop-03 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@hadoop-03 ~]# docker run -d -p 3443:3443 secfa/docker-awvs
9edae21cf46fae05833ce2c16557378cc2c7b8b5e41ee8e9db35566c16c2d2c5
```

# 5.访问

```
# 使用https  （这里我就是因为用的http协议访问网站所以死活访问不了）
https://xxx.xxx.xxx.xxx:3443/
username: admin@admin.com
password: Admin123
```

# 6.修改awvs管理员信息

我们拉取的secfa/docker-awvs是有默认的管理员用户名和密码的，虽然可以登陆进去改密码，但是想要改管理员用户邮箱呢

首先要进入容器

```
docker ps -a      //查看容器，找到相应id
docker exec -it id  /bin/bash      //进入容器
cd /home/acunetix/.acunetix
./change_credentials.sh
```

输入新的的管理员邮箱和密码
如果输错了，后退键删不了，ctrl+u即可（清除终端光标前的命令，对应的是ctrl+k，清除光标后的命令），如下图左边箭头是光标前，右边箭头是光标后



<img src="http://img.npfs06.top/20210816160952.png" style="zoom:80%;" />



最后会有这样的报错提示，但是不用管，修改其实是已经生效了的，也不用重启该容器，直接登陆



<img src="http://img.npfs06.top/20210816161004.png" style="zoom:80%;" />