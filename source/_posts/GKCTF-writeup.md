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



