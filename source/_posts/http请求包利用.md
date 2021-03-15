---
title: http请求包利用
date: 2020-04-11 10:20:07
updated: 2020-04-11 10:20:07
categories: web
---

HTTP请求包利用

<!--more-->

```
某些页面要求你必须要通过http://www.xxxx.com进行访问?

增加一个referer字段
Referrer:http://www.xxxx.com  (题目要求的url) 
```

```
题目要求必须是某个ip地址如127.0.0.1，才允许访问。

增加一个X-Forwarded-For字段
X-Forwarded-For：127.0.0.1
```

```
某些题目要求必须使用某个浏览器如HAHA浏览器，才允许访问。

增加User-Agent字段
User-Agent:HAHA
```

```
页面要求必须要安装了.net 9.0版本了才能访问。

修改User-Agent字段
User-Agent:.NET CLR 9
```

```
题目要求只能外国人才能访问这个页面。

修改Accept-Language字段
Accept-Language: en-US,en;q=0.8
```



```
还要做一点补充
请求方式不对的时候，可以尝试把GET改为POST、PUT等，会有意外的发现
```

