---
title: CTF bugku 论剑场 web20
date: 2020-04-13 23:26:38
updated: 2020-04-13 23:26:38
categories: Write up
---

最近经常做到过这种题目，写个笔记记录一下
一般这种题目，可以多刷新几次页面看看，且题目提示为动态密文

<!--more-->刷新页面可以发现每次动态密文都不同
题目说提交对应密文，python脚本走起

```python
import re
import requests
url="http://123.206.31.85:10020/"
s=requests.session()     
 #session对象是会话过程中，服务器用来记录特定用户会话信息的，它能够帮我们跨请求保持某些参数
r=s.get(url)               
#构造一个向服务器请求资源的url对象，在这里获取url链接
a=re.findall("[0-9a-z]+",r.content.decode("utf-8"))  
#返回r中与[0-9a-z]+相匹配的全部字符串，返回形式为数组;这里的.content是读取源文件字节流，随后将该字节流编码为Unicode编码
a=str(a[0])   
#re.findall提取出的信息是列表存储的，[0]即是将列表中的第一个数据从列表中提出来。
u=url+"?key="+a   
 #在url窗口的提交格式
r=s.get(url=u)       
 #get提交
print(r.content.decode("utf-8"))   

```

脚本多跑几遍，就可以得到flag



后来看了大佬的WP，发现动态密文的值是md5(时间戳+1)+一位随机数值

然后写脚本的时候加`for i in range(1,10):`,可以少跑几次,修改后的脚本如下：

```python
import re
import requests
for i in range(1,10):      #可以把range(1,10)这里的10改大点，这一一次直接跑出来的机率大点哈
    url="http://123.206.31.85:10020/"
    s=requests.session()
    r=s.get(url)
    a=re.findall("[0-9a-z]+",r.content.decode("utf-8"))
    a=str(a[0])
    u=url+"?key="+a
    r=s.get(url=u)
    print(r.content.decode("utf-8"))

```