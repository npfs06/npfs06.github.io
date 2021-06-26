---
title: hexo-blog-encrypt输入密码无反应
date: 2021-06-26 19:42:22
updated: 2021-06-26 19:42:22
categories: problems
---

通过hexp-blog-encrypt插件给hexo博客添加加密访问功能时遇到的问题及解决方案<!--more-->

很多时候，我们写的部分博客和文章是不希望别人看到的（比如一些情感相关的?），所以对这些文章进行加密，提供密码才能访问是个不错的方式。

Git Bash中输入命令行`npm install --save hexo-blog-encrypt`即可安装成功

**设置站点配置文件__config.yml：**

```
encrypt:
    password: 123456 //默认密码
```

然后在文章的头部添加上对应的password字段

```
---
title: hexo-blog-encrypt输入密码无反应
date: 2021-06-26 19:42:22
updated: 2021-06-26 19:42:22
categories: problems
password: 123456
---
```

当我安装好，准备测试下的时候，发现输入密码页面没有反应。查找之后发现是浏览器不支持http输入密码，需要把http 访问改为https访问。网上的教程基本上都是让买https证书，但是我嫌麻烦....

---

## 解决方案

想到我的博客是Github搭建的，而github可以直接设置强制https，一切问题迎刃而解

![](http://img.npfs06.top/20210626194934.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

![](http://img.npfs06.top/20210626195012.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

这里勾上

> hexo clean 
>
> hexo g -d

问题解决