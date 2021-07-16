---
title: WEB学习路线
date: 2021-07-11 20:30:16
updated: 2021-07-11 20:30:16
categories: web
password: 601601
---

暑期学习实验室web方向任务点<!--more-->

## 要求

每位同学至少选择两个知识点也可多个（**选择范围2-21**），在准备和大家分享之前，分享的同学需要准备至少两个题目，一个简单入门，一个偏难，需要ppt以及相应的文档发在群里以供各位同学复现，祝各位同学的技术能在暑期突飞猛进，有什么问题可以直接群里问我

## 工具

python、php、burpsuite、postman、dirsearch、御剑后台扫描工具、蚁剑、菜刀、hackbar、nmap、sqlmap、vps......



## 脚本

githack、hashpump、c-jwt-cracker、flask-session-cookie-manager、php-mt-seed-4.0、ds_store_exp_master、Gopherus-master......

## 基础

![](http://img.npfs06.top/20210711193443.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

## CTF方向

<a href="https://npfs06.top/2020/07/20/ctf中php常见知识点总结/" target="_blank">ctf中php常见知识点总结</a>

<a href="https://npfs06.top/2020/04/08/php%E9%BB%91%E9%AD%94%E6%B3%95/" target="_blank">ctfphp黑魔法</a>

1. **php弱类型** 
2. **php伪协议**
3. **php反序列化** <a href="https://npfs06.top/2020/04/24/php%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96/" target="_blank">php反序列化</a>
4. **php反序列化进阶-php反序列化字符逃逸**  <a href="https://npfs06.top/2020/07/19/php%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E5%AD%97%E7%AC%A6%E4%B8%B2%E9%80%83%E9%80%B8/" target="_blank">php反序列化字符逃逸</a>
5. **php回调后门**
6. **源码泄露**  <a href="https://npfs06.top/2020/04/21/web%E6%BA%90%E7%A0%81%E6%B3%84%E9%9C%B2/" target="源码泄露">源码泄露总结</a>
7. **条件竞争**（比如利用PHP_SESSION_UPLOAD_PROGRESS条件竞争进行文件包含）
8. **sql注入漏洞**（大知识点，可笼统介绍，也可以细分各个攻击方法，如万能密码、宽字节注入、堆叠注入、联合注入、报错注入、时间盲注、布尔盲注、二次注入、HTTP头注入、异或注入.....一步步深入，相应题目可以去sqli-labs上刷)
9. **XSS攻击**（CTF中xss重点为csp绕过（CISCN2019 华东北赛区 Web2），可不讲）
10. **文件上传漏洞**（要求包括各种情况的绕过,可以在upload-labs靶场练习）
11. **命令执行**（要求包括各种情况的绕过）  <a href="https://npfs06.top/2021/02/26/%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C%E7%BB%95%E8%BF%87%E5%B0%8F%E6%8A%80%E5%B7%A7/" target="_blank">命令执行</a>
12. **XXE攻击**  <a href="https://npfs06.top/2020/05/05/XXE%E6%B3%A8%E5%85%A5%E6%94%BB%E5%87%BB/" target="_blank">XXE攻击</a>
13. **SSRF（服务器端请求伪造）**
14. **格式化字符串漏洞**
15. **disable_function绕过**
16. **SSTI或者其他类型模版注入**  <a href="https://npfs06.top/2020/04/23/%E6%A8%A1%E6%9D%BF%E6%B3%A8%E5%85%A5%E5%B8%B8%E8%A7%81payload/" target="_blank">模板注入常见payload</a>
17. **python反序列化** <a href="https://npfs06.top/2021/04/29/python%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E/" target="_blank">python反序列化</a>
18. **JWT攻击**  <a href="https://npfs06.top/2020/07/27/jwt%E6%94%BB%E5%87%BB/" target="_blank">JWT攻击</a>
19. **php模版注入**
20. **php以及python伪随机数**
21. **文件包含漏洞**  <a href="https://npfs06.top/2020/04/09/%E5%B8%B8%E8%80%83%E6%96%87%E4%BB%B6%E5%8C%85%E5%90%AB%E6%BC%8F%E6%B4%9E/" target="_blank">文件包含漏洞</a>



## 进阶

1. **javascript框架**（比如  <a href="https://npfs06.top/2020/12/29/Nodejs%E5%8E%9F%E5%9E%8B%E9%93%BE%E6%B1%A1%E6%9F%93/" target="_blank">nodejs原型链污染</a>、vm.js 、vue.js）

2. **java反序列化**

3. **redis主从复制写shell**

4. **redis+ssrf**

5. **反弹shell**

6. **沙箱逃逸**

7. **各种框架漏洞**（比如spring、struts、thinkphp、twig、laravel、smarty）

   

   

## 简单漏洞学习

1. **csrf（跨站请求伪造）漏洞原理及利用**
2. **XSS漏洞（三种xss）**
3. **sql注入（sqlmap使用）**
4. **逻辑漏洞探测**
5. **文件上传漏洞**
6. **框架漏洞探测**
7. **弱口令漏洞**
8. **远程命令执行漏洞**



## 语言

html、css、js、python（requests等常见库使用）、php、java、go、nodejs



ctf需要的是全面的知识储备，这种储备不意味着会写代码，能码一个牛逼的网站就够的，以php举例，你不是要学会用php写网站，而是你得知道php的弱类型等php特有的问题。所以学代码是安全的入门，但是安全不是码代码，而是找问题。



## 其他

linux基础命令

<a href="https://npfs06.top/2020/04/23/linux%E6%96%87%E4%BB%B6%E5%92%8C%E7%9B%AE%E5%BD%95%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4/" target="_blank">linux基础命令</a> 

比如ls、cat.....

