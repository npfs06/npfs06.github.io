---
title: python反序列化漏洞
date: 2021-04-029 22:04:15
updated: 2021-04-29 22:04:15
categories: web
---

好久没做到过python反序列化的题目了，忘得差不多了。刚好在前几天的`[HFCTF 2021 Final]easyflask`遇到了python反序列化的相关考点，重新学习了一遍相关知识，特此记录<!--more-->

# pickle

pickle是python语言的一个标准模块，实现了基本的数据序列化和反序列化。pickle模块是以二进制的形式序列化后保存到文件中（保存文件的后缀为`.pkl`），不能直接打开进行预览。

## 序列化过程

- 从对象中提取所有属性（__dict__），并将属性转为键值对
- 写入对象的类名
- 写入键值对

## 反序列化过程

- 获取 pickle 输入流
- 重建属性列表
- 根据保存的类名创建一个新的对象
- 将属性复制到新的对象中

| 函数  |               说明               |
| :---: | :------------------------------: |
| dumps |     对象反序列化为bytes对象      |
| dump  | 对象反序列化到文件对象，存入文件 |
| loads |       从bytes对象反序列化        |
| load  |  对象反序列化，从文件中读取数据  |

下面，我们举一个例子来说明其工作方式：

```python
//python3
import pickle

class name():
    def __init__(self):
        self.name = 'npfs'
        self.date = 123456

flag = name()
a = pickle.dumps(flag)
print(a)
b = pickle.loads(a)
print(b.name)
```

输出结果为:
```
b'\x80\x04\x952\x00\x00\x00\x00\x00\x00\x00\x8c\x08__main__\x94\x8c\x04name\x94\x93\x94)\x81\x94}\x94(h\x01\x8c\x04npfs\x94\x8c\x04date\x94J@\xe2\x01\x00ub.'
npfs
```

可以看到，我们成功通过反序列化的方式恢复了之前我们序列化进去的类对象并成功的执行了对象的方法.
我们需要注意以下两点:

 **1.**  如果我在反序列化以前删除了name()这个类，那么我们在反序列化的过程中因为对象在当前的运行环境中没有找到这个类就会报错，从而反序列化失败。
 **2.**  对于我们自己定义的class，如果直接以形如下面这种形式:

```
class name():
    name = 'npfs'
    date = 123456
```

那么序列化时这两个数据将不会被打包,只有以上面第一个例子中一样写一个\__init__方法才能被进行打包.



# 反序列化的底层实现

## PVM

这里简单了解一下就可以了

PVM(Python 虚拟机)是实现Pickle反序列化最本质的东西。在反序列化的过程中，我们可以把它理解成字符串经过PVM处理后，被转化成一个对象的过程。 而字符串本身就是一串PVM指令。 Pickle实际上是一门栈语言，他有不同的几种编写方式，pickle构造出的字符串，有很多个版本。在pickle.loads时，可以用Protocol参数指定协议版本，例如指定为0号版本：

```
class name():
    def __init__(self):
        self.date = 123456

flag = name()
a = pickle.dumps(flag,protocol=0)
```



PVM 由三部分组成：

- 指令处理器

  从流中读取 opcode 和参数，并对其进行解释处理。重复这个动作，直到遇到 . 这个结束符后停止。

  最终留在栈顶的值将被作为反序列化对象返回。

- stack

  由 Python 的 list 实现，被用来临时存储数据、参数以及对象。

- memo

  由 Python 的 dict 实现，为 PVM 的整个生命周期提供存储。

PS：注意下 stack、memo 的实现方式，方便理解下面的指令。

> 当前用于 pickling 的协议共有 5 种。使用的协议版本越高，读取生成的 pickle 所需的 Python 版本就要越新。
>
> - v0 版协议是原始的 “人类可读” 协议，并且向后兼容早期版本的 Python。
> - v1 版协议是较早的二进制格式，它也与早期版本的 Python 兼容。
> - v2 版协议是在 Python 2.3 中引入的。它为存储 [new-style class](https://docs.python.org/zh-cn/3/glossary.html#term-new-style-class) 提供了更高效的机制。欲了解有关第 2 版协议带来的改进，请参阅 [PEP 307](https://www.python.org/dev/peps/pep-0307)。
> - v3 版协议添加于 Python 3.0。它具有对 [bytes](https://docs.python.org/zh-cn/3/library/stdtypes.html#bytes) 对象的显式支持，且无法被 Python 2.x 打开。这是目前默认使用的协议，也是在要求与其他 Python 3 版本兼容时的推荐协议。
> - v4 版协议添加于 Python 3.4。它支持存储非常大的对象，能存储更多种类的对象，还包括一些针对数据格式的优化。有关第 4 版协议带来改进的信息，请参阅 [PEP 3154](https://www.python.org/dev/peps/pep-3154)。

### 指令集

> 本文重点说明 0 号协议，不明白的指令建议直接看对应实现！

```
MARK           = b'('   # push special markobject on stack
STOP           = b'.'   # every pickle ends with STOP
POP            = b'0'   # discard topmost stack item
POP_MARK       = b'1'   # discard stack top through topmost markobject
DUP            = b'2'   # duplicate top stack item
FLOAT          = b'F'   # push float object; decimal string argument
INT            = b'I'   # push integer or bool; decimal string argument
BININT         = b'J'   # push four-byte signed int
BININT1        = b'K'   # push 1-byte unsigned int
LONG           = b'L'   # push long; decimal string argument
BININT2        = b'M'   # push 2-byte unsigned int
NONE           = b'N'   # push None
PERSID         = b'P'   # push persistent object; id is taken from string arg
BINPERSID      = b'Q'   #  "       "         "  ;  "  "   "     "  stack
REDUCE         = b'R'   # apply callable to argtuple, both on stack
STRING         = b'S'   # push string; NL-terminated string argument
BINSTRING      = b'T'   # push string; counted binary string argument
SHORT_BINSTRING= b'U'   #  "     "   ;    "      "       "      " < 256 bytes
UNICODE        = b'V'   # push Unicode string; raw-unicode-escaped'd argument
BINUNICODE     = b'X'   #   "     "       "  ; counted UTF-8 string argument
APPEND         = b'a'   # append stack top to list below it
BUILD          = b'b'   # call __setstate__ or __dict__.update()
GLOBAL         = b'c'   # push self.find_class(modname, name); 2 string args
DICT           = b'd'   # build a dict from stack items
EMPTY_DICT     = b'}'   # push empty dict
APPENDS        = b'e'   # extend list on stack by topmost stack slice
GET            = b'g'   # push item from memo on stack; index is string arg
BINGET         = b'h'   #   "    "    "    "   "   "  ;   "    " 1-byte arg
INST           = b'i'   # build & push class instance
LONG_BINGET    = b'j'   # push item from memo on stack; index is 4-byte arg
LIST           = b'l'   # build list from topmost stack items
EMPTY_LIST     = b']'   # push empty list
OBJ            = b'o'   # build & push class instance
PUT            = b'p'   # store stack top in memo; index is string arg
BINPUT         = b'q'   #   "     "    "   "   " ;   "    " 1-byte arg
LONG_BINPUT    = b'r'   #   "     "    "   "   " ;   "    " 4-byte arg
SETITEM        = b's'   # add key+value pair to dict
TUPLE          = b't'   # build tuple from topmost stack items
EMPTY_TUPLE    = b')'   # push empty tuple
SETITEMS       = b'u'   # modify dict by adding topmost key+value pairs
BINFLOAT       = b'G'   # push float; arg is 8-byte float encoding

TRUE           = b'I01\n'  # not an opcode; see INT docs in pickletools.py
FALSE          = b'I00\n'  # not an opcode; see INT docs in pickletools.py
```

这里有几个需要重点了解一下

 **c** : 读取本行的内容作为模块名(module),读取下一行的内容作为对象名(object),然后将 module.object作为可调用对象压入到栈中。
**(** : 将一个标记对象压入到栈中,用于确定命令执行的位置,该标记常常搭配t指令一起使用,以便产生一个元组。
**0** ：弹出栈项的元素并丢弃。
**S** : 后面跟字符串,PVM会读取引号中的内容,直到遇见换行符,然后将读取到的内容压入到栈中。(结果要有\n分隔)
**0** ：弹出栈项的元素并丢弃
**t** : 从栈中不断弹出数据,弹射顺序与压栈时相同,直到弹出左括号.此时弹出的内容形成了一个元组,然后,该元组会被压入栈中。
**R** : 将之前压入栈中的元组和可调用对象全部弹出,然后将该元组作为可调用参数的对象并执行该对象,最后将结果压入到栈中。
**b** : 使用栈中的第一个元素（储存多个属性名: 属性值的字典）对第二个元素（对象实例）进行属性设置
**.**  : 结束整个Pickle反序列化过程。

## 如何生成 pickle？

### 手写

基本模式：

```
c<module>
<callable>
(<args>
tR
```

看个小例子：

```
cos
system
(S'ls'
tR.

<=> __import__('os').system(*('ls',))

# 分解一下：
cos
system  =>  引入 system，并将函数添加到 stack

(S'ls'  =>  把当前 stack 存到 metastack，清空 stack，再将 'ls' 压入 stack
t       =>  stack 中的值弹出并转为 tuple，把 metastack 还原到 stack，再将 tuple 压入 stack
# 简单来说，(,t 之间的内容形成了一个 tuple，stack 目前是 [<built-in function system>, ('ls',)]
R       =>  system(*('ls',))
.       =>  结束，返回当前栈顶元素
```



# \__reduce__

 `__reduce__() `魔术方法类似于 PHP 对象中的 `__wakeup()` 方法，会在反序列化时自动调用`__reduce__() `魔术方法可以返回一个字符串或者时一个元组。简单说来,就是如果当`__reduce__`返回值为一个元组(2到5个参数),第一个参数是可调用(callable)的对象,第二个是该对象所需的参数元组.在这种情况下,反序列化时会自动执行\__reduce__里面的操作.其中返回元组时，第一个参数为`一个可调用对象`，第二个参数为`该对象所需要的参数`


例：

```python
import pickle
import os

class Rce(object): 
    def __reduce__(self):
        return (os.system,('ls',))

a = Rce()
b = pickle.dumps(a)
pickle.loads(b)  # 执行该语句进行反序列化，自动执行 __reduce__ 方法，并且执行 os.system('ls')
```

