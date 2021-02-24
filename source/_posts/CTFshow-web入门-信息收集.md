---
title: CTFshow_web入门_信息收集
date: 2020-10-10 15:17:26
categories: web入门
---

CTFshow web入门之信息收集篇

<!--more-->

![](http://img.npfs06.top/1999159-20201031182159184-2133244042.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





## Web1

```
右键
查看源码得到flag
```

## Web2

```
Ctrl+U或者 view-source:http://网站/ 查看源码得到flag
```

## Web3

响应头获取flag

拿burpsuite抓包看响应头，或者之间谷歌F12   network看响应头

## Web4

访问 robots.txt     **http://url/robots.txt**

有个/flagishere.txt目录，访问下得到flag       **http://url/flagishere.txt**

## Web5     

**注：web5 --web10 考察点均为源码泄露，可参考文章    <a href='https://www.cnblogs.com/NPFS/p/12746349.html' target='_blanket' >源码泄露</a>**

题目提示 phps源码泄露有时候能帮上忙

直接访问 index.phps

得到源码，就能看到flag

## Web6

拿dirsearch目录扫描，扫到www.zip，访问得到压缩包

在index.php中提示 flag in fl000g.txt   ，但是我们在flooog.txt 无法发现正确flag

正确做法是在页面中访问fl000g.txt   ,得到flag        **http://url/fl000g.txt**

## Web7

dirsearch扫描

![](http://img.npfs06.top/1999159-20200903230827601-1559053931.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

发现/.git/泄露，访问下就得到flag了

## Web8

考察点   svn泄露

做法同上，访问 **http://url/.svn**页面直接得到flag



## Web9

考察点：  vim 临时文件泄露

```
程序员使用vim编辑器编写一个`index.php`文件时，会有一个`.index.php.swp`文件，如果文件正常退出，则该文件被删除，如果异常退出，该文件则会保存下来，该文件可以用来恢复异常退出的index.php，同时多次意外退出并不会覆盖旧的`.swp`文件，而是会生成一个新的，例如`.swo`文件。
```

做法同上 ，访问**http://url/index.php.swp**,下载文件，得到flag



## Web10

flag在cookie那

![](http://img.npfs06.top/1999159-20200903231734977-350417039.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## Web11

http://www.jsons.cn/nslookup/

![](http://img.npfs06.top/20210223150506.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





## Web12

dirsearch扫描，后台登录，需要账号密码

![](http://img.npfs06.top/20210223150522.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



账号：admin   

密码的话，结合题目提示：有时候网站上的公开信息，就是管理员常用密码

很容易找到密码

![](http://img.npfs06.top/20210223150600.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

登入即可得到flag



## Web13



![](http://img.npfs06.top/20210223150622.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

document文件翻到最下面，就有后台地址和登入账号密码，登入即可得到flag

![](http://img.npfs06.top/20210223150635.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





## Web14

后台目录扫描，有个/editor

url访问，胡乱按几个按钮，每到要输入URL的地方，就会有个谜一样的链接提示

![](http://img.npfs06.top/20210223150656.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

访问后：

![](http://img.npfs06.top/20210223150710.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

base64两次解密，得到的是一个错误的flag。。。。



正确做法：

![](http://img.npfs06.top/20210223150726.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

点这个文件空间，可以得到所有目录

flag在 var/www/html/nothinghere/fl000g.txt

url 访问 /nothinghere/fl000g.txt 得到flag



## Web15

还是一样，扫描目录，登录后台  /admin

用户名admin   ，选择忘记密码

![](http://img.npfs06.top/20210223150739.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

西安，得到修改后的密码，登入即可得到flag



## Web16

既然题目提示了探针，那就访问下 tz.php 看下，看下PHP参数

![](http://img.npfs06.top/20210223150750.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



点下如图PHPINFO，跳转到phpinfo页面，搜索得到flag

## Web17

![](http://img.npfs06.top/20210223150807.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## Web18

查看源码，访问下Js文件

![](http://img.npfs06.top/20210223150853.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

访问110.php  

得到flag



## Web19

查看源码

```
</script>
    <!--
    error_reporting(0);
    $flag="fakeflag"
    $u = $_POST['username'];
    $p = $_POST['pazzword'];
    if(isset($u) && isset($p)){
        if($u==='admin' && $p ==='a599ac85a73384ee3219fa684296eaa62667238d608efa81837030bd1ce1bf04'){
            echo $flag;
        }
}
    -->
```

抓包修改下password值为a599ac85a73384ee3219fa684296eaa62667238d608efa81837030bd1ce1bf04，即可得到flag



## web20

mdb文件是早期asp+access构架的数据库文件 直接查看url路径添加/db/db.mdb 下载文件通过txt打开或者通过EasyAccess.exe打开搜索flag 

![](http://img.npfs06.top/20210223150908.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



<--------本文完-------->