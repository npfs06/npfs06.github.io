---
title: GKCTF writeup
date: 2021-06-28 09:05:48
updated: 2021-06-28 09:05:48
categories: Write up
---

y11由于比赛和期末考试周冲突到了就没去打，现在考试结束了特来复现<!--more-->

## [GKCTF 2021]easycms

admin.php存在后台登入

账号admin

密码12345

登入后在主题出存在主题导出，查看导出链接

> http://b5d12352-545d-4461-a8e3-47732d9fc7ce.node4.buuoj.cn/admin.php?m=ui&f=downloadtheme&theme=L3Zhci93d3cvaHRtbC9zeXN0ZW0vdG1wL3RoZW1lL2RlZmF1bHQvaXkyOC56aXA=

后面那串解码发现为

<img src="http://img.npfs06.top/20210716091827.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:67%;" />

直接改为/flag ，base64加密后下载即为flag文件





## [GKCTF 2021]babycat

sign up时alert了

<img src="http://img.npfs06.top/20210717104628.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:67%;" />

通过查看源代码，知道注册信息的组成，可以抓包绕过alert,进行注册

data={"username":"admin","password":"123"}

<img src="http://img.npfs06.top/20210717104329.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:67%;" />

注册后，发现role为guest,没有upload权限，提示需要role为admin

<img src="http://img.npfs06.top/20210717104531.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:67%;" />



不过在Download Test处发现存在任意文件下载  ,可以读web.xml -> class ,没发现利用点javaweb 目录

```
/WEB-INF/web.xml：Web应用程序配置文件，描述了 servlet 和其他的应用组件配置及命名规则。
/WEB-INF/classes/：含了站点所有用的 class 文件，包括 servlet class 和非servlet class，他们不能包含在 .jar文件中
/WEB-INF/lib/：存放web应用需要的各种JAR文件，放置仅在这个应用中要求使用的jar文件,如数据库驱动jar文件
/WEB-INF/src/：源码目录，按照包名结构放置各个java文件。
/WEB-INF/database.properties：数据库配置文件
```

在注册页面的源代码钟，role是控制权限的，而且在代码处可能存在拼接，这里尝试拼接json进行注入。

data={“username”:”123″,”password”:”123″,”\u0072\u006F\u006C\u0065″:”admin”,”123″:{“role”:”guest”}}                     

![](http://img.npfs06.top/20210717114805.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

注册成功，变成admin，这里可以进入upload进行上传文件

通过之前的报错，知道网站是tomcat，可以传jsp🐎

默认目录无法上传，前面图片存在的位置是../../static/，所以尝试来个目录穿越

<img src="http://img.npfs06.top/20210717134613.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:67%;" />



<img src="http://img.npfs06.top/20210717134551.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:67%;" />