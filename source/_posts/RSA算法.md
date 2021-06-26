---
title: RSA算法
date: 2020-07-27 18:19:39
updated: 2020-07-27 18:19:39
categories: Crypto
---

rsa算法及其常见攻击方法总结<!--more-->

# RSA 算法

1.**质数（素数)**是指在大于1的自然数中，除了1和它本身以外不再有其他因数的自然数。 
2.**合数**是指比1大但不是素数的数
3.**约数（因数）**整数a除以整数b(b≠0) 除得的商正好是整数而没有余数，我们就说a能被b整除，或b能整除a。a称为  b的倍数，b称为a的约数
4.**互质数**：如果两个整数a,b的最大公因数（greatest common divisor）为1，即gcb(a,b)=1，那么称a,b两数互质
5.**欧拉函数**是指设m为正整数，则1,2,3,4.......,m中与m互素的整数的个数记为φ(m)，叫做欧拉函

---
## RSA加解密涉及变量

```
N(n):模数（modulus）

p 和 q ：N的两个因子（factor）

e 和 d：(密钥) 互为模反数的两个指数（exponent）

c 和 m：分别是密文和明文，这里一般指的是一个十进制的数还有一个就是n的欧拉函数值

欧拉函数值：r

pow(x, y, z)：效果等效pow(x, y)1 % z， 先计算x的y次方，如果存在另一个参数z，需要再对结果进行取模。
```



---

## RSA 密钥流程

```
1.选择两个大的参数，计算出模数 N = p * q

2.计算欧拉函数 φ = (p-1) * (q-1)，然后选择一个e(1<e<φ)，并且e和φ互质（互质：公约数只有1的两个整数）

3.选一个整数e,满足条件1<e<φ(m),且gcd(φ(m),e)=1。

4.取e的模反数d，计算方法为:e * d ≡ 1 (mod φ) （模反元素：如果两个正整数e和n互质，那么一定可以找到整数d，使得 e * d - 1 被n整除，或者说e * d被n除的余数是1。这时，d就叫做e的“模反元素”。欧拉定理可以用来证明模反元素必然存在。两个整数a,b，它们除以整数M所得的余数相等：a ≡ b(mod m)，比如说5除3余数为2，11除3余数也为2，于是可写成11 ≡ 5(mod 3)。）

5.对明文m进行加密：c = pow(m, e, N),可以得到密文c。

6.对密文c进行解密：m = pow(c, d, N),可以得到明文m。

7.以{e,n}为公开密钥，{d,n}为秘密密钥。

```



          **对于RSA加密算法，公钥{e，n}为公钥，可以任意公开，破解RSA最直接（亦或是暴力）的方法就是分解整数N，然后计算欧拉函数φ(n)=(p-1) * (q-1),再通过d * e ≡ 1 mod φ(N)，即可计算出 d，然后就可以使用私钥{d,n}通过m = pow(c,d,N)解密明文。**



# 常见攻击方法

##  **已知`p`、`q`、`e`或者已知`n`、`e`求出`d`**

```python
import gmpy2
p = gmpy2.mpz(18443)#初始化大整数
q = gmpy2.mpz(49891)
e = gmpy2.mpz(19)
phi_n = (p-1)*(q-1)
d = gmpy2.invert(e,phi_n)  # invert（e，r）返回d使得e * d == 1 mod r，如果不存在d，则返回0
print("p={0},q={1},e={2}".format(p,q,e))
print("d is:\n%s"%d)

```

注：gmpy2：开源的高精度算数运算库<https://blog.csdn.net/x_yhy/article/details/83903367>

        分解`Ｎ`得到`p` `q`可以通过在线网站http://www.factordb.com/index.php

## **已经求出`d`、`n`、`c`，然后可以求出相应的明文m**

```
#求明文
import gmpy2
n = 920139713   #模数
d = 96849619    #密钥
c = """
704796792
752211152
274704164
...  #密文
"""
result = ""
c_list = c.split()
#print(c_list)
for i in c_list:
    result += chr(pow(int(i),d,n))
print(result)

```

## **已知`c`、`e`、`n`求m**

结合以上两种方法，在知道`n`的前提下可求·`p`、`q`,利用`p`、`q`、`e`可以求出`d`，，从而因为已知`d`、`n`、`c`，求出相应的明文m



---

##  **利用n的公约数**

当题目给出若干个模数n1,n2......,且模数很大。如果两次加密的`n1`和`n2`具有相同的素因子，那么我们可以利用`欧几里德算法`直接分解`n1`和`n2`.从而计算出两个`n`的最大公约数`p`：

>素因子的定义：对于一个数n来说，将它的因子拆到若干个素数相乘，这些素数被称为n的素因子。
>比如 12可以被拆为2 6
>6不是质数，可以继续拆为2*3
>所以最后12的素因子就是 2, 3（不计重复元素）

识别此类题目，通常会发现题目给了若干个n，均不相同，并且都是2048bit，4096bit级别，无法直接分解http://www.factordb.com/index.php，并且明文都没什么联系，e也一般取65537。

![](https://img2020.cnblogs.com/blog/1999159/202007/1999159-20200712082911929-729980764.png)

```
#-*-coding:utf-8-*-
'''
求两个数的最大公约数
算法参考:https://zhidao.baidu.com/question/36550887.html
by:reborn
'''
import gmpy2
n1=
n2=
def gys1(n1,n2):    #辗转相除法(欧几里德算法)
    if n1<n2:
        n1,n2=n2,n1
    while n2!=0:
        temp=n1%n2
        n1=n2
        n2=temp
    return n1
def gys2(n1,n2):    #更相减损法
    while n1!=n2:
        if n1<n2:
            n1,n2=n2,n1
        temp=n1-n2
        n1=temp
    return n1
p=gys2(n1,n2)
print ("p=",p)

#求q1,q2

q1=n1//p
q2=n2//p
print("q1=",q1)
print("q2=",q2)

#求d_1,d_2

p0 = gmpy2.mpz(p)#初始化大整数
q_1 = gmpy2.mpz(q1)
q_2 = gmpy2.mpz(q2)
e = gmpy2.mpz(65537)
r_1 = (p0-1)*(q_1-1)
r_2 = (p0-1)*(q_2-1)
d_1 = gmpy2.invert(e,r_1)  # invert（e，r）返回d使得e * d == 1 mod r，如果不存在d，则返回0
d_2 = gmpy2.invert(e,r_2)
print("d_1=",d_1)
print("d_2=",d_2)

# 求c1,c2

c1=
c2=
m1 = pow(c1, d_1, n1)
m2 = pow(c2, d_2, n2)
print("m1=",m1)
print("m2=",m2)

```

根据`欧几里德算法`算出的`p`之后，再用`n`除以`p`即可求出`q`，由此可以得到的参数有`p`、`q`、`n`、`e`，再使用常规方法计算出`d`，即可破解密文。

m = pow(c, d, N),可以得到明文m





----

## **共模攻击**

如果在RSA的使用中使用了相同的模n对相同的明文m进行了加密，那么就可以在不分解n的情况下还原出明文m的值。

> c1 = m^e1 mod n
> c2 = m^e2 mod n

识别：非常简单，若干次加密，每次n都一样，明文根据题意也一样即可。



---

##  **已知私钥文件、c求m** 

题目中给出了私钥文件private.pem和flag.enc 

pem文件通常是包含了-----BEGIN PRIVATE KEY-----和-----END PRIVATE KEY-----，是 Base64 编码的二进制内容
使用私钥解密密文的方式

使用openssl工具 
利用如下命令：

> rsautl -decrypt -in flag.enc(密文名称) -inkey private.pem



---

## **已知公钥文件、c求m**

题目中给出了public.pem和密文flag.enc

> openssl rsa -pubin -text -modulus -in warmup -in pubkey.pem                     
>   [提取出pubkey.pem中的参数]

> 得到n，化为十进制

> 将n分解为P，q

> python rsatool.py -o private.pem -e 65537 -p 275127860351348928173285174381581152299 -q 319576316814478949870590164193048041239
>
> [使用rsatool生成私钥文件: private.pem]

> openssl rsautl -decrypt -in flag.enc -inkey private.pem



---

## **低加密指数攻击**

在RSA中e也称为加密指数。由于e是可以随意选取的，选取小一点的e可以缩短加密时间，但是选取不当的话，就会造成安全问题。

**e=3时的小明文攻击**

当e=3时，如果明文过小，导致明文的三次方仍然小于n，那么通过直接对密文三次开方，即可得到明文。

>(1)m^3<n,也就是说m^3=c;
>(2)m^3>n，即(m^3+kn)mod n=c（爆破k，不知道k取什么值）。



- 第一种情况   根据 `c = pow(m, e, N) `可知：

当e=3时，如果明文过小，导致**明文的三次方**仍然小于n，那么通过直接对密文三次开方，即可得到明文。

- 第二种情况  如果**明文的三次方**比n大，但是不够大，那么设k，有：    c=(m^3+kn)mod n

爆破k，如果（c-kn）能开三次根式，那么可以直接得到明文。



**识别：**

推荐在e=3的时候首先尝试这种方法。



>  openssl  rsa  -pubin  -in  pubkey.pem  （读取公钥内容）
>  openssl  rsa   -pubin  in  pubkey.pem -text（以文本格式输出公钥内容),从这一步可以知道e的值

从而判断为低加密指数攻击



---

## **低加密指数广播攻击**

低加密指数广播攻击，即如果选取的加密指数较低，并且使用了相同的加密指数给一个接收者发送了相同的信息（或者给一群接收者发送了相同的信息），那么可以进行广播攻击得到明文。

假如我们需要将一份明文进行多份加密，但是每份使用不同的密钥，密钥中的模数n不同但指数e相同且很小，我们只要拿到多份密文和对应的n就可以利用[中国剩余定理](https://www.cnblogs.com/freinds/p/6388992.html)进行解密。

 **适用**

只要满足以下情况，我们就可以考虑实用低加密指数广播攻击：

> 1.加密指数e非常小
> 2.一份明文使用不同的模数n，相同的加密指数e进行多次加密
> 3.可以拿到每一份加密后的密文和对应的模数n、加密指数e



低加密指数广播攻击脚本

```
# coding:utf8

from struct import pack,unpack
import zlib
import gmpy
def my_parse_number(number):
    string = "%x" % number
    #if len(string) != 64:
    #    return ""
    erg = []
    while string != '':
        erg = erg + [chr(int(string[:2], 16))]
        string = string[2:]
    return ''.join(erg)
def extended_gcd(a, b):
    x,y = 0, 1
    lastx, lasty = 1, 0
    while b:
        a, (q, b) = b, divmod(a,b)
        x, lastx = lastx-q*x, x
        y, lasty = lasty-q*y, y
    return (lastx, lasty, a)
def chinese_remainder_theorem(items):
  N = 1
  for a, n in items:
    N *= n
  result = 0
  for a, n in items:
    m = N/n
    r, s, d = extended_gcd(n, m)
    if d != 1:
      N=N/n
      continue
      #raise "Input not pairwise co-prime"
    result += a*s*m
  return result % N, N
  //中国剩余定理 ， 输入多组c和多组n，以及较小的指数e
sessions=[
{"c": , "e": , "n": },
{"c": , "e": , "n": },
{"c": , "e": , "n": }
]

data = []
for session in sessions:
    e=session['e']
    n=session['n']
    msg=session['c']
    data = data + [(msg, n)]
print "Please wait, performing CRT"
x, n = chinese_remainder_theorem(data)
e=session['e']
realnum = gmpy.mpz(x).root(e)[0].digits()
print my_parse_number(int(realnum))
```

---

## **n可以分解为多个素数**


使用公钥加密和使用私钥解密流程（中国剩余定理）：
准备
首先，我们需要在在生成私钥公钥时，多生成几个数：
我们的d是e对phi(n)的逆元，我们现在需要另外2个逆元（分别是对(p-1)和(q-1)的），既
1：计算dp，使得dp * e = 1 mod(p-1)
2：计算dq，使得dq * e = 1 mod(q-1)
此外需要第三个元素，既q对p的逆元
3：计算qInv，使得qInv * q = 1 mod p
        1 2 3 都作为私钥的一部分。

> dp = d mod p-1
> dq = d mod q-1

**计算：**

使用公钥加密：
若要加密明文m,则需要计算c = m^e mod n，c为密文。

使用私钥解密：
1:m1=c^dp mod p
2:m2=c^dq mod q
3:h= (qInv*((m1 - m2)mod p)) mod p
4:m = m2 + h*q
m就是明文。

例：n=17947
           e=3
           c=8363
           m=???

```
import gmpy2
n=17947
p=137
q=131
e=3
c=8363
dp=gmpy2.invert(e,p-1)
dq=gmpy2.invert(e,q-1)
m1=pow(c,dp,p)
m2=pow(c,dq,q)
qInv=gmpy2.invert(q,p)
h=(qInv*((m1-m2)% p)) % p
m=m2+h*q
print(m)
```

**多素数**

例:n=p1*p2*p3=2279269
     p1=137              
     p2=131
     p3=127
     e=19

```
预先计算:
dp = 19^-1 mod 137-1 = 43
dq = 19^-1 mod 131-1 = 89
dr = 19^-1 mod 127-1 = 73

若要解密密文 768924，则先计算
1:m1=768924^43 mod 137 = 102
2:m2=768924^89 mod 131 = 120
3:m3=768924^73 mod 127 = 5

等式1与等式2连列方程组计算：
qInv = 114
h = (qInv*((m1 - m2)mod p)) mod p = 3
m12 = m2 + h*q = 120 + 3*131 = 513

所以等式1与等式2的通用解为：513+k1*(131*137)
所以结合等式3问题可以变为：
m1=513  p=17947
m2=5    q=127
qInv*q≡ 1 mod p    ——>qInv=10316
h = (10316*((513 - 5)mod 17947)) mod 17947 =4
m = 5 + 4*127 = 513
......

```

jiaoben

```
import gmpy2
n=2279269
p1=137
p2=131
p3=127
e=19
c=768924
dp1=gmpy2.invert(e,p1-1)
dp2=gmpy2.invert(e,p2-1)
dp3=gmpy2.invert(e,p3-1)
m1=pow(c,dp1,p1)
m2=pow(c,dp2,p2)
m3=pow(c,dp3,p3)
qInv1=gmpy2.invert(p2,p1)
h1=(qInv1*((m1-m2) % p1)) % p1
m4=m2+h1*p2
p4=p1*p2
qInv2=gmpy2.invert(p3,p4)
h2=(qInv2*((m4-m3)% p4)) % p4
m=m3+h2*p3
print(m)
```

---

## **dp、dq泄露**

> dp = d mod p-1
> dq = d mod q-1

这种参数是为了让解密的时候更快速而产生的

已知p,q,dp,dq,c求m

```
import gmpy2
import binascii
import libnum
def decrypt(dp,dq,p,q,c):
    InvQ = gmpy2.invert(q,p)
    mp = pow(c,dp,p)
    mq = pow(c,dq,q)                   #求幂取模运算
    m=(((mp-mq)*InvQ)%p)*q+mq          #求明文公式
    print (binascii.unhexlify(hex(m)[2:]))
    print(libnum.n2s(m))
p = 
q = 
dp = 
dq = 
c = 
decrypt(dp,dq,p,q,c)

```

**已知e,n,dp,c求m**

```
import gmpy2
import libnum
import binascii
def getd(n,e,dp):
    for i in range(1,e):            #在范围(1,e)之间进行遍历
        if (dp*e-1)%i == 0:
            if n%(((dp*e-1)/i)+1)==0:    #存在p，使得n能被p整除
                p=((dp*e-1)/i)+1
                q=n/(((dp*e-1)/i)+1)
                phi = (p-1)*(q-1)         #欧拉定理
                d = gmpy2.invert(e,phi)%phi        #求模逆
                return d
e =
n = 
dp = 
c = 
d=getd(n,e,dp)
m=pow(c,d,n)                            #快速求幂取模运算
print(binascii.unhexlify(hex(m)[2:]))       #16进制转文本
print(libnum.n2s(m))

```

[https://blog.csdn.net/qq_42939527/article/details/105202716](https://blog.csdn.net/qq_42939527/article/details/105202716)



---

## **已知n,r求p,q**

核心是通过n和r解出p和q

```
1.二分法，求得p，q

2.RSATool2v17中，输入p，q，r，e，得到d  (脚本也可)

3.通过m=pow(c,d,n)
注意：有时题目有要求，解出的可能是m乘上某一个参数，这是需要仔细审题

4.转字符，得到flag

```

脚本

```
import gmpy2
import numpy as np
np.set_printoptions(suppress=True)

n=gmpy2.mpz(14057332139537395701238463644827948204030576528558543283405966933509944444681257521108769303999679955371474546213196051386802936343092965202519504111238572269823072199...)
r=gmpy2.mpz(14057332139537395701238463644827948204030576528558543283405966933509944444681257521108769303999679955371474546213196051386802936343092965202519504111238572269823072199...)

c1=n-r+1
print (c1)

l=c1/2
r=c1
#p=(l+r)/2
#y=p*(c1-p)

while l<r:
	p=(l+r)/2
	y=p*(c1-p)
	if y==n:
		print (p)
		break
	if y>n:
		print (y>n)
		l=p
	else:
		print (y<n)
		r=p
		print ("done")
q=c1-p
print q
/*
if p>q:
    p,q=q,p
factor2 = 2021 * p + 2020 * q
if factor2 < 0:
    factor2 = (-1) * factor2
    
_P=sympy.nextprime(factor2)
*/

```