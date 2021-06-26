---
title: DelCTF2019 SSRF Me
date: 2020-07-17 18:17:25
updated: 2020-07-17 18:17:25
categories: Write up
---

第一次审计这么长的代码，很值得记录一下<!--more-->

# [De1CTF 2019]SSRF Me

以下是审计过程中的一些笔记

![](https://img.npfs06.top/20210326205524.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326205534.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](https://img.npfs06.top/20210326205544.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

```
#! /usr/bin/env python
#encoding=utf-8
from flask import Flask
from flask import request
import socket
import hashlib
import urllib
import sys
import os
import json
reload(sys)
sys.setdefaultencoding('latin1')

app = Flask(__name__)

secert_key = os.urandom(16)


class Task:
    def __init__(self, action, param, sign, ip):
        self.action = action
        self.param = param
        self.sign = sign
        self.sandbox = md5(ip)
        if(not os.path.exists(self.sandbox)):          #SandBox For Remote_Addr
            os.mkdir(self.sandbox)
#_init_初始化

    def Exec(self):
        result = {}
        result['code'] = 500
        if (self.checkSign()):

#第一个if：如果checkSign(self) 返回 True ，则进入下一个if
#审计checkSign(self)

            if "scan" in self.action:
                tmpfile = open("./%s/result.txt" % self.sandbox, 'w')
                resp = scan(self.param)
                if (resp == "Connection Timeout"):
                    result['data'] = resp
                else:
                    print resp
                    tmpfile.write(resp)
                    tmpfile.close()
                result['code'] = 200
#如果scan在action里面，则我们可以让param进入scan这个函数，并的目录下创建一个result.txt 
#然后通过scan()函数把名字为param的网址里的内容写到result.txt中，由于param是可控的，所以很容易想到这里把flag.txt传给param。

            if "read" in self.action:
                f = open("./%s/result.txt" % self.sandbox, 'r')
                result['code'] = 200
                result['data'] = f.read()
            if result['code'] == 500:
                result['data'] = "Action Error"
        else:
            result['code'] = 500
            result['msg'] = "Sign Error"
        return result
#如果read在action里面，则我们可以读取读取result.txt的内容赋值给result

     def checkSign(self):
        if (getSign(self.action,self.param) == self.sign):
            return True
        else:
            return False
#如果getSign(self.action, self.param)和self.sign相等则返回True ,否则返回False


#generate Sign For Action Scan.
@app.route("/geneSign", methods=['GET', 'POST'])
def geneSign():
    param = urllib.unquote(request.args.get("param", ""))
    action = "scan"
    return getSign(action, param)
#提取get方法传入的，参数名叫param对应得值，并将其url编码后赋值给param
#将scan赋值给action
#审计 getSign()

@app.route('/De1ta',methods=['GET','POST'])
def challenge():
    action = urllib.unquote(request.cookies.get("action"))
    param = urllib.unquote(request.args.get("param", ""))
    sign = urllib.unquote(request.cookies.get("sign"))
    ip = request.remote_addr
    if(waf(param)):
        return "No Hacker!!!!"
    task = Task(action, param, sign, ip)
    return json.dumps(task.Exec())
#以get方法传入param参数值，在cookie里面传递action和sign的值
#使param绕过waf，审计waf
#用我们传进去的 action 、 param  、sign  、ip 这四个参数构造一个Task类对象，并且执行它的Exec方法
#审计Task
@app.route('/')
def index():
    return open("code.txt","r").read()


def scan(param):
    socket.setdefaulttimeout(1)
    try:
        return urllib.urlopen(param).read()[:50]
    except:
        return "Connection Timeout"



def getSign(action, param):
    return hashlib.md5(secert_key + param + action).hexdigest()
#将secert_key 、param 、 action这三个值连接起来后进行md5加密，并将其作为十六进制数据字符串值返回

def md5(content):
    return hashlib.md5(content).hexdigest()


def waf(param):
    check=param.strip().lower()
    if check.startswith("gopher") or check.startswith("file"):
        return True
    else:
        return False
#移除param头尾指定的字符(默认为空格或换行符)，并将param中中所有大写字符转化为小写
#若param以gopher或file为前缀，返回True,否者返回False
#回到challenge()

if __name__ == '__main__':
    app.debug = False
    app.run(host='0.0.0.0')
```

**总结**

审计从路由开始，然后在慢慢延申出去

这里有三个路由：

- /geneSign
- /De1ta
- /

---



- 从/De1ta开始看起，首先是创建了一个Task的类，action、sign的值是由cookie得到，而param的值就是直接通过GET方法传递param参数的值得到，ip就是你的ip地址，接着param参数会经过waf，如果过了waf，则执行这个类的**Exec**。

* 顺着这个思路，我们追溯到waf这个方法上,通过审计我们知道要绕过waf，param的值不能以 gopher和file开头

* 接下去执行Task里的Exec方法，通过审计我们发现如果checkSign(self) 为真 ，则可以传递/De1ta页面的param参数进入到scan方法，并的目录下创建一个result.txt ，然后通过scan()函数把参数param的值写到result.txt中，由于param是可控的，所以很容易想到这里把flag.txt传给param。

* 审计checkSign(self) 函数，发现如果getSign(cookie传入的action, get传入的param)和cookie传入的sign相等则返回True ,否则返回False

* 审计getSign(),我们发现不知道secert_key的值，但是路由/geneSign有一个return getSign(scan, param)，这里我们另/geneSign页面的参数param的值为`flag.txtread`(这里为什么后面会讲到)，通过getSign得到的sign值即为md5(secert_key + 'flag.txtread' + 'scan')

* 回到Task类的Exec方法`if "read" in self.action:`如果read在action里面，则我们可以读取读取result.txt的内容赋值给result，这里result.txt的值实际上是我们传入的param的值

* 在这里就可以解释为什么/geneSign页面我们传入的param的值要为flag.txtread了，因为结合Exec方法，我们要实现写入文件和读出的功能，就必须另//De1ta页面的action为readsacn或scanread,此时的getSign(),返回的值就是hashlib.md5(secert_key + flag.txt + readscan).hexdigest(),而此时只有另/geneSign页面的param参数为flag.txtread才能使

> getSign(self.action, self.param) == getSign(flag.txt+readscan) ,
>
> 即md5(secret_key+flag.txtread+scan) == md5(secret_key+flag.txt+readscan)



---

所以这里总的做法就是在/geneSign页面get  ?param=flag.txtread,获得mds值，而这个值其实是等于/De1ta页面的sign值的

![](https://img.npfs06.top/20210326205558.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

所以第二步就是在/De1ta页面，get ?param=flag.txt   ,cookie  action=readscan  ,sign=我们在/geneSign页面得到的md5值，这样就可以得到flag了