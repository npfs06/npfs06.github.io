---
title: CTFshow_web入门_爆破
date: 2020-10-11 15:24:34
categories: web入门
---

CTFshow web入门之爆破

<!--more-->

![](http://img.npfs06.top/20210223152515.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)




## web21

先随便输入账号密码，抓包

![](http://img.npfs06.top/20210223152534.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



base64解密，可以发现账号密码的形式为   账号：密码，这里我们采用 Custom iterator进行爆破

使用方法可参考：<a href='https://www.cnblogs.com/007NBqaq/p/13220297.html' target='_blank'>Custom iterator的使用</a>

![](http://img.npfs06.top/20210223152601.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](http://img.npfs06.top/20210223152616.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](http://img.npfs06.top/20210223152635.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](http://img.npfs06.top/20210223152650.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





爆破即可得到flag

![](http://img.npfs06.top/20210223152708.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## web22

子域名爆破

flag.ctfer.com



## web23

```php
<?php

error_reporting(0);

include('flag.php');
if(isset($_GET['token'])){
    $token = md5($_GET['token']);
    if(substr($token, 1,1)===substr($token, 14,1) && substr($token, 14,1) ===substr($token, 17,1)){
        if((intval(substr($token, 1,1))+intval(substr($token, 14,1))+substr($token, 17,1))/substr($token, 1,1)===intval(substr($token, 31,1))){
            echo $flag;
        }
    }
}else{
    highlight_file(__FILE__);

}
?>

```

直接在源代码上改改，写个脚本就好了 (假设是三位的数)

```
<?php
error_reporting(0);
$string = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
print (strlen($string)."\n");

for ($a = 0; $a < 62; $a++) {
    for ($b = 0; $b < 62; $b++) {
        for ($c = 0; $c < 62; $c++) {
            $flag = $string[$a] . $string[$b] . $string[$c];
            $token = md5($flag);
            if (substr($token, 1, 1) === substr($token, 14, 1) && substr($token, 14, 1) === substr($token, 17, 1)) {
                if ((intval(substr($token, 1, 1)) + intval(substr($token, 14, 1)) + substr($token, 17, 1)) / substr($token, 1, 1) === intval(substr($token, 31, 1))) {
                    echo $flag . "\n";
                }
            }
        }
    }
}
```

解出来答案很多

![](http://img.npfs06.top/20210223152726.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



随便GET传参一个即可得到flag



## web24

考察点：伪随机数

了解伪随机数可参考此文<a href='https://blog.csdn.net/zss192/article/details/104327432' target='_blank'>伪随机数</a>

```php
<?php

error_reporting(0);
include("flag.php");
if(isset($_GET['r'])){
    $r = $_GET['r'];
    mt_srand(372619038);
    if(intval($r)===intval(mt_rand())){
        echo $flag;
    }
}else{
    highlight_file(__FILE__);
    echo system('cat /proc/version');
}
```

![](http://img.npfs06.top/20210223152748.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

GET传参即可



## web25

```php
<?php

error_reporting(0);
include("flag.php");
if(isset($_GET['r'])){
    $r = $_GET['r'];
    mt_srand(hexdec(substr(md5($flag), 0,8)));
    $rand = intval($r)-intval(mt_rand());
    if((!$rand)){
        if($_COOKIE['token']==(mt_rand()+mt_rand())){
            echo $flag;
        }
    }else{
        echo $rand;
    }
}else{
    highlight_file(__FILE__);
    echo system('cat /proc/version');
}
```





```php
$rand = intval($r)-intval(mt_rand());
```

根据这句，可以通过传入的`r`得到第一个随机数，之后我们可以通过工具`php_mt_seed `<a href='https://github.com/lepiaf/php_mt_seed' target='_blank'>地址</a>

进行逆推，得到种子

![](http://img.npfs06.top/20210223152840.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

通过抓包查看响应头我们可以知道该php版本为7.3.11，因此可能的种子就是如图两种

>    if($_COOKIE['token']==(mt_rand()+mt_rand()))

通过该行代码我们知道要将两个随机数相加，脚本如下

![](http://img.npfs06.top/20210223152857.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



burp抓吧修改即可

![](http://img.npfs06.top/20210223152912.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**注**：

关于这里为什么要?r=1819399594,因为

```php
 if((!$rand)){
        if($_COOKIE['token']==(mt_rand()+mt_rand())){
            echo $flag;
        }
```

只有在$rand不存在时，$_COOKIE['token']==(mt_rand()+mt_rand() 才能执行，而1819399594是我们在传?r=0时页面输出的随机数

```php
 $rand = intval($r)-intval(mt_rand());
```

此时的$rand = mt_rand()即第一个生成的随机数，因此只要我们使$r=mt_rand()=1819399594，就能让$rand=0，不存在，从而执行接下来的代码




## web26

在安装页面，查看源码，可以发现js代码

![](http://img.npfs06.top/20210223152937.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

在checkdb.php页面POST即可

![](http://img.npfs06.top/20210223152952.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## web27

打开页面是正方教务系统，仔细点看下面有个录取名单，和 学生学籍信息查询系统

点录取名单，内容如下

![](http://img.npfs06.top/20210223153008.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

可以看到身份证号不完全，少了生日（年月日），这里要爆破

![](http://img.npfs06.top/20210223153019.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

爆破出来，学生学籍信息查询系统 查询之后会提示，教务系统用户名，以及登入初始密码为身份证

登入即可得到flag

![](http://img.npfs06.top/20210223153033.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





## web28

![](http://img.npfs06.top/20210223153055.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



看到这个url，诡异的0和1，尝试爆破

![](http://img.npfs06.top/20210223153106.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

<a href='https://blog.csdn.net/qq_39101049/article/details/90234669' target="_blank">burpsuite爆破的四种模式</a>

这里要把2.txt去掉

使用Cluster bomb模式爆破从0-100的数字




![](http://img.npfs06.top/20210223153119.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)