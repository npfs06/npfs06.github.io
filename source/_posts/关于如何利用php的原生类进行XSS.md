---
title: 关于如何利用php的原生类进行XSS
date: 2020-07-27 20:52:53
updated: 2020-07-27 20:52:53
categories: web
---

在CTF中，这一类型的题目一般会在代码中给出反序列化点，但是却找不到pop链<!--more-->

# 关于如何利用php的原生类进行XSS

类似于`[BJDCTF 2nd]xss之光`这一题，通过GitHack下载代码后，发现如下：

![](https://img.npfs06.top/20210326210034.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

我们可以看到题目代码给出了反序列化点，却没有给出pop链，这个时候就可以考虑到利用php的原生类进行XSS

---

我们首先需要了解一下PHP序列化及其反序列化的魔术方法

**__ toString __：当对象被当作一个字符串使用时候调用(不仅仅是echo的时候，比如file_exists()判断也会触发)**

这里主要讲两个Php的内置类：Error和Exception

## Error

适用于php7版本

Error类是php的一个内置类，用于自动自定义一个Error，因为它内置有一个toString的方法，在php7的环境下可能会造成一个xss漏洞。

利用方法：

![](https://img.npfs06.top/20210326210053.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](https://img.npfs06.top/20210326210101.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](https://img.npfs06.top/20210326210114.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

我们可以看到成功执行了我们构造的恶意代码





## Exception

适用于php5、7版本

这个类利用的方式和原理和Error 类相同，只是适用范围更广

利用方法：

![](https://img.npfs06.top/20210326210121.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](https://img.npfs06.top/20210326210128.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326210153.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



可以看到恶意代码同样被执行了