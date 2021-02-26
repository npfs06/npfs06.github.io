---
title: MD5相关
date: 2021-02-19 10:32:44
categories: web
---

**string强等于**

<!--more-->

```
<h2>Md5 Revenge Now!</h2>
<!--
if((string)$_POST['param1']!==(string)$_POST['param2'] && md5($_POST['param1'])===md5($_POST['param2']))
{
        die("success!);
 } -->
```

payload:

```text
param1=o%BC%FA%5C%0EiG%CA%1C%D7%DB%B4%E0%9B%FCF%A78%0Aj%18%B5%C3Q%0C%9A%82%CE%27%A4Cf%40%B1%FC%D6%DC%8D%DF%05%EB%B9%DF%5B%18%88%D4%A6%05%956%BC%EC%3E%90%0F%26%FA%2C%AA%21%25%20g%A7%DB%EA%DB%89%05%A7%07%0D%14dS%20S%FB%90%B5%8A%C4T%E5%B2L%20%95%1C6%CD%17N%CE%80%7B%9C%1E%8DN%26%1A%3A%11%BA%9E%B4%11%BD%04%99%0F%E1%9D%C4%D3%E2%D8%9E%B8%E6%7F%B3%E9%06
&param2=o%BC%FA%5C%0EiG%CA%1C%D7%DB%B4%E0%9B%FCF%A78%0A%EA%18%B5%C3Q%0C%9A%82%CE%27%A4Cf
```

```
param1=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%00%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1U%5D%83%60%FB_%07%FE%A2&param2=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%02%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1%D5%5D%83%60%FB_%07%FE%A2
```

```
param1=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2&param2=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%02%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%d5%5d%83%60%fb%5f%07%fe%a2
```

11230178，md5值为0e732639146814822596b49bb6939b97







## 双md5结果仍为0e开头字符串大全

```
CbDLytmyGm2xQyaLNhWn
md5(CbDLytmyGm2xQyaLNhWn) => 0ec20b7c66cafbcc7d8e8481f0653d18
md5(md5(CbDLytmyGm2xQyaLNhWn)) => 0e3a5f2a80db371d4610b8f940d296af


770hQgrBOjrcqftrlaZk
md5(770hQgrBOjrcqftrlaZk) => 0e689b4f703bdc753be7e27b45cb3625
md5(md5(770hQgrBOjrcqftrlaZk)) => 0e2756da68ef740fd8f5a5c26cc45064


7r4lGXCH2Ksu2JNT3BYM
md5(7r4lGXCH2Ksu2JNT3BYM) => 0e269ab12da27d79a6626d91f34ae849
md5(md5(7r4lGXCH2Ksu2JNT3BYM)) => 0e48d320b2a97ab295f5c4694759889f


```



## $md5=md5($md5)

```
md5('0e215962017') ==> “0e291242476940776845150308577824”
```





## php中md5($str,true)注入

```
ffifdyop
4SV7p
bJm4aG
bNas5p
ckHAEb
```





## MD4

```
if ($_GET["hash1"] != hash("md4", $_GET["hash1"]))
{
    die('level 1 failed');
}
```

0e251288019
0e898201062

## MD2

```php

<?php
for($i=0;$i<99999;$i++){
$x1=hash("md2", '0e'.$i.'024452');
if(substr($x1,0,2)==='0e'  and is_numeric($x1)){
break;
}
}
for($j=0;$j<999999;$j++){
$x2=hash('md2',hash("md2", '0e'.$j.'48399'));
if(substr($x2,0,2)==='0e'  and is_numeric($x2)){
break;
}
}
print('b=0e'.$i.'024452&c=0e'.$j.'48399');
?>

```

b=0e652024452&c=0e603448399





## shal

 if(sha1($v1)==sha1($v2) && $v1!=$v2){

aaroZmOk
aaK1STfY
aaO8zKZF
aa3OFF9m