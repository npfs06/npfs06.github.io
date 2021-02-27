---
title: 利用PHP_SESSION_UPLOAD_PROGRESS进行文件包含
date: 2020-10-10 19:55:50
categories: web
---

利用PHP_SESSION_UPLOAD_PROGRESS进行文件包含
<!--more-->

![](http://img.npfs06.top/20210226195757.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

我们可以利用`session.upload_progress`将木马写入session文件，然后包含这个session文件。不过前提是我们需要创建一个session文件，并且知道session文件的存放位置。

session里有一个默认选项，**session.use_strict_mode**默认值为off。

![](http://img.npfs06.top/20210226195810.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

此时用户是可以自己定义Session ID的。比如，我们在Cookie里设置PHPSESSID=flag，PHP将会在服务器上创建一个文件：/tmp/sess_flag”。即使此时用户没有初始化Session，PHP也会自动初始化Session,并产生一个键值.

注：在Linux系统中，session文件一般的默认存储位置为 /tmp 或 /var/lib/php/session

但是**session.upload_progress.cleanup**默认是开启的

![](http://img.npfs06.top/20210226195829.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



框着的这句话意思就是说**在默认情况下，session.upload_progress.cleanup是开启的，一旦读取了所有POST数据，它就会清除进度信息**

这里我们可以利用条件竞争来进行文件上传

下面讲个例题来实践一下：

CTFshow 里web入门里的一个文件包含题

![](http://img.npfs06.top/20210226195841.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



通过观察代码，可以看到过滤了大部分的文件包含函数，这里我们利用PHP_SESSION_UPLOAD_PROGRESS加条件竞争进行文件包含

以POST的形式发包，传的文件随意

```
<!DOCTYPE html>
<html>
<body>
<form action="http://e113b1bc-28b8-4f08-9e60-b74fe3a96ef3.chall.ctf.show/" method="POST" enctype="multipart/form-data">
    <input type="hidden" name="PHP_SESSION_UPLOAD_PROGRESS" value="123" />
    <input type="file" name="file" />
    <input type="submit" value="submit" />
</form>
</body>
</html>
```

抓包，这里我们添加一个 Cookie :PHPSESSID=flag   ，PHP将会在服务器上创建一个文件：/tmp/sess_flag” （这里我们猜测session文件默认存储位置为/tmp），并在PHP_SESSION_UPLOAD_PROGRESS下添加一句话木马，修改如下

![](http://img.npfs06.top/20210226195857.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



因为我们在上面这个页面添加的ID值是flag，所以传参?file=/tmp/sess_flag

修改如下：这个a是随便加的，主要是为了方便爆破

![](http://img.npfs06.top/20210226195917.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



条件竞争，将POST和GET的包都开启爆破，即可得到目录，

![](http://img.npfs06.top/20210226195933.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



可以看到有fl0g.php，只要把ls改为 cat fl0g.php即可，修改如下：

![](http://img.npfs06.top/20210226195952.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

爆破即可得到flag

![](http://img.npfs06.top/20210226200007.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

