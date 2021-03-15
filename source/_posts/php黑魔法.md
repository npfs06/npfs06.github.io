---
title: php黑魔法
date: 2020-04-08 09:36:24
updated: 2020-04-09 09:36:24
categories: web
---

php中常见的黑魔法与其绕过方法

<!--more-->

# 弱比较

若字符串以数字开头，则取开头数字作为转换结果，若无则输出0

```php
<?php 
echo "---------------- 弱比较黑魔法 -------------------------";
echo "<br>";echo "<br>";
if (1 == "1abc") {
	if (0 == "abc") {
		if ("0e132456789" == "0e7124511451155") {
			#常见于md5弱比较绕过
			echo "两个'=='的弱比较成功";
		}
	}
}
```



# md5(),sha1()

md5()是不能处理数组的，md5(数组)会返回null，两个null相等绕过
sha1()也是同理

```php
<?php 
echo "---------------- md5(),sha1()黑魔法 -------------------------";
echo "<br>";echo "<br>";
if (md5($md5_bug1[]=1) === md5($md5_bug2[]=1)){
	if (sha1($md5_bug1[]=1) === sha1($md5_bug2[]=1)){
		echo "md5传入数组绕过成功";
	}
}
```



# 转换

php会自动进行转换，比如16进制，科学计数法等，有时也用这点绕过

```php
<?php 
echo "---------------- 转换黑魔法 -------------------------";
echo "<br>";echo "<br>";
echo "0x1e240 自动转换10进制：",0x1e240;
echo "<br>";
echo "123456 十进制数：",123456;
echo "<br>";
echo "1e240 科学计数法自动转换：",1e240;
```



# strcmp()

int strcmp ( string $str1 , string $str2 )
参数 str1第一个字符串。str2第二个字符串。
如果 str1 小于 str2 返回 < 0；
如果 str1 大于 str2 返回 > 0；
如果两者相等，返回 0。

strcmp()函数只有在相等的情况下返回0。
php在5.3版本之前若传入的是一个非字符串类型数据，比如数组和对象，则会报错，但在报错的同时会返回0，那么我们传入一个数组，它会返回NULL，而判断使用了== ，但是NULL==0是bool(true)，这样就成功绕过。

```php
<?php
echo "---------------- strcmp()黑魔法 -------------------------";
echo "<br>";echo "<br>";
$strcmp_bug[]=1;
if (@strcmp($strcmp_bug, "you_don't_know_this_string") == 0){
	echo "strcmp()数组黑魔法绕过成功";
}
```

## 例题

```
<?php
    $password="***************"
     if(isset($_POST['password'])){
        if (strcmp($_POST['password'], $password) == 0) {
            echo "Right!!!login success";
            exit();
        } else {
            echo "Wrong password..";
        }
?>
```

只要我们$_POST[‘password’]是一个数组或者一个object即可

```
password[]=admin
```



# intval()

intval()转换的时候，会将从字符串的开始进行转换知道遇到一个非数字的字符。
即使出现无法转换的字符串，intval()不会报错而是返回0。
注：
在科学计数法字符串转换为数字时，如果 E 后面的数小于某个值会弄成 double 类型，再强制转换为 int 类型时可能会有奇妙的结果，测试发现某变量为 1e-1000 时已经可以触发这个漏洞绕过两个检查，使得某变量既大于 0 又不大于 0。
例如：

```
var_dump((int)('1e-1000')>0);
var_dump('1e-1000'>0);
```

结果

```
Command line code:1:
bool(true)
Command line code:1:
bool(false)
```

再入：

```
var_dump((int)('1e-10')>0);
var_dump('1e-10'>0);
```

结果

```
Command line code:1:
bool(true)
Command line code:1:
bool(true)
```

```php
<?php
echo " ---------------- intval()黑魔法 -------------------------";
echo "<br>";echo "<br>";
echo "2 的intval()转换结果：",intval('2');
echo "<br>";
echo "3abcd 的intval()转换结果：",intval('3abcd');
echo "<br>";
echo "abcd 的intval()转换结果：",intval('abcd'); 
echo "<br>";echo "<br>";
$intval_bug = '1e-1000';
if ((int)($intval_bug) > 0) {
	if ($intval_bug <= 0) {
		echo "intval()转换问题绕过成功";
	}
}
```



# ereg()

字符串对比解析，ereg函数存在NULL截断漏洞，当ereg读取字符串string时,如果遇到了%00,后面的字符串就不会被解析。
注：这里的%00是需要urldecode才可以截断的，这是url终止符，且%00长度是1不是3

```php
<?php
echo "---------------- ereg()黑魔法 -------------------------";
echo "<br>";echo "<br>";
$input = urldecode('1e8%00*-*');
// echo strlen($input); #7
if (isset($input)){
	if (@ereg ("^[a-zA-Z0-9]+$",$input) === FALSE){
		echo $input;
        echo '输入只能是数字和字母！';
    }
	else{
		echo "ereg()%00截断绕过成功";   
	}
}
```

## 例题

```
<?php
if (isset ($_GET['password'])) {
   if (ereg ("^[a-zA-Z0-9]+$", $_GET['password']) === FALSE)
   {
       echo '<p>You password must be alphanumeric</p>';
   }
   else if (strlen($_GET['password']) < 8 && $_GET['password'] > 9999999)
   {
       if (strpos ($_GET['password'], '*-*') !== FALSE)
       {
           die('Flag: ' . $flag);
       }
       else
       {
           echo('<p>*-* have not been found</p>');
       }
   }
   else
   {
       echo '<p>Invalid password</p>';
   }
}
?>
```

ereg函数存在NULL截断漏洞,可以%00截断，则不会读取后面的内容，可以绕过输入*-*
大小和长度限制可以利用科学计数法

```
?password=1e8%00*-*
```

# is_numeric()

当有两个is_numeric判断并用and连接时，and后面的is_numeric可以绕过
16进制也可以绕过is_numeric()检验，可以用来绕过sql注入里的过滤

```php
<?php
echo "---------------- is_numeric()黑魔法 -------------------------";
echo "<br>";echo "<br>";
$is_numeric1 = "0123";
$is_numeric2 = "abc";
$c=is_numeric($is_numeric1) and is_numeric($is_numeric2);

if ($c) {
	if (is_numeric('    +.1234e5678')) {
		echo "is_numeric()连用漏洞绕过";
		# 这样也能绕过检测……
	}
}
```



# switch()

当switch没有break时可以继续往下执行。
这里也有自动转换，比如$switch_bug = a,会当0执行，=1a，会当1执行……

```php
<?php
echo "---------------- switch()黑魔法 -------------------------";
echo "<br>";echo "<br>";
@$switch_bug = 0;
if (isset ( $switch_bug )) {
	switch ($switch_bug) {
 		case 0 :
 				echo '你已经运行到了case0';
 				echo "<br>";
 		case 1 :
 				echo '你已经运行到了case1';
 				echo "<br>";
 		case 2 :
 				echo '你已经运行到了case2';
 				echo "<br>";
 				echo "switch()没有break连续运行绕过成功";
 				break;
 		default :
 				echo "1";
 				break;
 }
}

```



# array_search()

用到了PHP弱类型的一个特性，当一个整形和一个其他类型行比较的时候，会先把其他类型intval再比。
当检索中带入字符串，比如”sky”，会intval(‘sky’)==0，从而致使数字数组也可以查询成功

```php
<?php
echo "---------------- array_search()黑魔法 -------------------------";
echo "<br>";echo "<br>";
$array_search=[1,0];
$eee = @array_search("XMAN", $array_search);
if($eee){
	echo "array_search()检索字符串绕过成功";
}
```



# json_decode()

这个漏洞迷一样……我也是偶然发现，只能自己从下面的样例代码中领悟了

```php
<?php
echo "---------------- json_decode()黑魔法 -------------------------";
echo "<br>";echo "<br>";
$v3=0;
$input = '{"key":0001}';
$b=json_decode(@$input);
if($var = $b === NULL){
        ($var === true)?$v3=1:NULL;
        if ($v3) {
        	echo "json_decode()函数漏洞绕过成功";
        }
    }
?>
```



# Extract（）

*该函数使用数组键名作为变量名，使用数组键值作为变量值。但是当变量中有同名的元素时，该函数默认将原有的值给覆盖掉。这就造成了变量覆盖漏洞。*

例：

```
<?php
$a = 1;    //原变量值为1
$b = array('a' => '3');
extract($b);    //经过extract()函数对$b处理后
echo $a;    //输出结果为3
?>
```

## 例题

```
"extract($_GET);
if(isset($a))
{
$content=trim(file_get_contents($flag));//file_get_contents—将整个文件读入一个字符串
if($a==$content)                             //trim—去除字符串首尾处的空白字符（或者其他字符）
{ echo'flag{**********}'; }
else
{ echo'这不是flag啊'; }
}"
```

题目使用了**extract($_GET)**接收了GET请求中的数据，并将键名和键值转换为变量名和变量的值，然后再进行两个if 的条件判断，所以可以使用GET提交参数和值，利用**extract()**对变量进行覆盖，从而满足各个条件。

```
?a=&content=
或者可以利用file_get_contents（）可以读取只读流：php://input构造Payload
```



# in_array()绕过（适用不设第三个参数的情况）

> bool in_array ( mixed $needle , array $haystack [, bool $strict = FALSE ] )
> 说明：在 haystack 中搜索 needle，如果没有设置 strict 则使用 宽松 的比较。如果第三个参数 strict 的值为 true，则 in_array 函数还会检查 needle 的 类型 是否和 haystack 中的相同。

in_array 函数存在第三个参数 strict，它用来标记函数在对两元素进行比较时是否采用 严格比较，类似 == 和 === 区别，in_array 函数默认采用 宽松 比较，即不比较类型，只比较值是否相等。
例如：`in_array(0, array('s'))`会返回 TRUE,这是因为因为 in_array 函数没有使用第三个参数，而默认进行了 宽松 比较，即等同于 0 == 's' ，而为什么0 == 's'，可参照下表:

![](https://img.npfs06.top/20210305101134.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

参照第四点，当两不同类型数值中含有数字类型，都会转化为数字类型进行比较。那么s转化为数字类型为0，所以0 == 0 ？判断为 true。



# filter_var()  （仅对FILTER_VALIDATE_URL）

> filter_var() 要求传入参数符合url规定，函数通过指定的过滤器过滤一个变量。 如果成功，则返回被过滤的数据。如果失败，则返回 FALSE。
> FILTER_VALIDATE_URL：把值作为 URL 来验证。

该函数的具体使用方法可参考[http://www.findme.wang/blog/detail/id/473.html](http://www.findme.wang/blog/detail/id/473.html)

FILTER_VALIDATE_URL 过滤器来判断是否是一个合法的url。

## 例题

```
// index.php
<?php 
$url = $_GET['url'];
if(isset($url) && filter_var($url, FILTER_VALIDATE_URL)){
    $site_info = parse_url($url);
    if(preg_match('/sec-redclub.com$/',$site_info['host'])){
        exec('curl "'.$site_info['host'].'"', $result);
        echo "<center><h1>You have curl {$site_info['host']} successfully!</h1></center>
              <center><textarea rows='20' cols='90'>";
        echo implode(' ', $result);
    }
    else{
        die("<center><h1>Error: Host not allowed</h1></center>");
    }

}
else{
    echo "<center><h1>Just curl sec-redclub.com!</h1></center><br>
          <center><h3>For example:?url=http://sec-redclub.com</h3></center>";
}

?>
```

构造符合函数要求的URL即可，又因为题目正则匹配了要求以sec-redclub.com结尾，构造如下：

```
?url=0://xxx.com;sec-redclub.com  
?url=http://xxx.com()sec-redclub.com
()里换成@&?/,:#这些任意一个均可
```



# .htmlentities() 

在写PHP代码时，不能在字符串中直接写实体字符，因此需要一个将HTML特殊字符转换成实体字符的函数 htmlentities()。
注：htmlentities() 并不能转换所有的特殊字符，是转换除了空格之外的特殊字符，且单引号和双引号需要单独控制（通过第二个参数）。第2个参数取值有3种，分别如下：

- ENT_COMPAT（默认值）：只转换双引号。
- ENT_QUOTES：两种引号都转换。
- ENT_NOQUOTES：两种引号都不转换。

![](https://img.npfs06.top/20210305101332.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





参考 <a href="https://skysec.top/2017/07/22/PHP%E5%87%BD%E6%95%B0%E9%BB%91%E9%AD%94%E6%B3%95%E5%B0%8F%E6%80%BB%E7%BB%93/" target="_blank">PHP函数黑魔法小总结/ </a>

