---
title: BSidesCF 2019 Mixer
date: 2020-07-30 21:20:36
updated: 2020-07-30 21:20:36
categories: Write up
---

很秀的一道题，需要密码学的知识，学到了<!--more-->

第一步，登入，登入成功后提示要使`is_admin?`为1，所以我们要做的就是让其为1

![](https://img.npfs06.top/20210405212337.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

抓包，修改下cookie的user (这里为什么要修改cookie，也许题目最开始的那行小字就是提示吧。。`Note: the "signature" and "rack.session" cookies are not part of the challenge!`)

扩展：这里需要了解一下<a href='https://zh.wikipedia.org/wiki/%E5%88%86%E7%BB%84%E5%AF%86%E7%A0%81%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F' target='_blanket'>分组密码工作模式</a>

本题主要是CBC加密

![](http://img.npfs06.top/20210405212346.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

我们通过修改user,将一组16进制对调，可以发现报错，多尝试几次，可以推测出解码为

> {"first_name":"admin","last_name":"admin","is_admin":0}

所以我们需要的就是构造

> {"first_name":"admin","last_name":"admin","is_admin":1}

这里需要知道CBC一般是16字节为一个块

我们只要构造：

```
{"first_name":"A
1.00000000000000
","last_name":"x
xxx","is_admin":
0}
一共5组
```

然后将第2组移到第5组之前，即

```
{"first_name":"A1.00000000000000","last_name":"paww","is_admin":1.000000000000000}
```

而 1.000000000000000 == 1 ，即可成功绕过admin登入

![](http://img.npfs06.top/20210405212354.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

抓包

![](https://img2020.cnblogs.com/blog/1999159/202007/1999159-20200730112645201-2057796280.png)

这里的user为

> 74ba95cdeaef9e38fdd0543be7873714ede75aa3f199aec8b4cfdee00084b153713bc8a476f7029e75a30074d7ad2414e95d4866feba88df47630671eb7806b43c8bf4d5fa9ff12561cdd044cf52941c

因为CBC是16字节为一组，转为16进制就是32个字符为一块，所以第二块就是

[32:64]

所以写个脚本即可

![](https://img.npfs06.top/20210405212402.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

将变换后的cookie替换user的值即可得到flag

![](https://img.npfs06.top/20210405212416.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





