---
title: HTML
date: 2020-04-05 22:44:21
updated: 2020-04-05 22:44:21
categories: 前端
---

之前学html时做的笔记<!--more-->

# 实现0秒跳转

```html
<meta http-equiv="refresh" content="0; http://fishc.taobao.com">
```



---

# “自适应” PC和手机

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```





---

# CSS简单设置颜色
```html
<style type="text/css">                 
     h1 {color: red}    
     p {color: blue}    
     a {                
     	color: yellow;     
     	background: black; 
    	}                  
</style>           
```





---

# 背景图片

```html
 <style>
   body {                                 
     	background-image: url("../img/bc.png") 
      }                                      
 </style>
```



---

# 视图界面大小

```html
<style media="screen and (min-width:512px) and (max-width:1024px)">
    body {
        background-image:url("../img/1.png")
    }
</style>
```



---

# CSS外链

```html
<link rel="stylesheet" type="text/css" href="styles.css">
 <link rel="stylesheet" type="text/css" media="print" href="print.css"> 
<link rel="stylesheet" type="text/css" media="screen and (min-width:512px) and (max-width:1024px)" href="screen512to1024.css"> 
<link rel="icon" type="image/x-icon" href="../img/favicon.ico"> |
```



---

# 基础路径

```html
<head>
    <base href="http://demo.npfs.com/html5/lesson4/" target="_blank">
</head>
```





---

# js外链

```html
<script type="text/javascript" src="hi.js"></script>
```



---

# noscript

```html
<noscript>
    <p>
        你的浏览器不支持javascript 哦！
    </p>
</noscript>
```





---

#  < span >

```html
<head>
    <style type="text/css">
        span {
            color:red;
        }
    </style>
</head>
<body>
 	<p>黑夜给了我黑色的<span>眼睛</span></p>
</body>
```



---



# < pre >

原样输出



---

#  < code > 标签是一个短语标签，用来定义计算机代码文本。

\<code> 标签是一个短语标签，用来定义计算机代码文本。



---

# < q >

\<q>标签定义短的引用。

浏览器经常在引用的内容周围添加引号。



---

# < blockquote> 

< blockquote>标签定义摘自另一个源的块引用。

浏览器通常会对 < blockquote> 元素里所以内容进行缩进。



---

# < abbr >
```html
<body>
<p><abbr title="鱼C工作室">FishC.com</abbr> was founded in 2010.</p>
</body>
```

显示效果：FishC.com下有虚线，鼠标停留会提示：鱼C工作室



---

# < dfn>

```html
<body>
<p><dfn>HTML</dfn>是一门用于创建网页的标准标记语言。</p>
</body>
```

显示效果：斜字体



---

# < address>

```html
<body>
<address>
<strong>联系我们</strong><br>
邮箱：<a href="mailto:fishc_service@126.com">fishc_service@126.com</a><br>
旺旺：dingdingjiayu<br>
微信：fishc_studio（公众号）<br>
</address>
</body>
```

显示效果：整段斜字体



---

# < ruby>

```html
<body>
<ruby>魑<rp>（</rp><rt>chī</rt><rp>）</rp></ruby>
<ruby>魅<rp>（</rp><rt>mèi</rt><rp>）</rp></ruby>
<ruby>魍<rp>（</rp><rt>wǎng</rt><rp>）</rp></ruby>
<ruby>魉<rp>（</rp><rt>liǎng</rt><rp>）</rp></ruby>
</body
```

显示效果：给字上方加注音

> ruby 元素由一个或多个字符（需要一个解释/发音）和一个提供该信息的 rt 元素组成，还包括可选的 rp 元素，定义当浏览器不支持 "ruby" 元素时显示的内容。



---

# < strong> / < b>

都能使文本变粗



---

# < em> / < i>

\<em>标签告诉浏览器把其中的文本表示为强调的内容。

显示效果：用斜体来显示。

\<i>元素也可以让内容*倾斜*，只不过它没有附带任何表示强调的语义



---

# 使用css代替b和i元素

```html
<head>
<style type="text/css">
        .bold {font-weight: bolder;}
        .italic {font-style: italic;}
    </style>
</head>
<body>
<p class="bold">这里的内容都加粗会比较好看！</p>
<p class="italic">这里的内容还是倾斜的好……</p>
</body>
```



---

# < del>  / < s>

显示效果：将文章划掉



---

# < ins> / < u>

显示效果：将文章加下划线（实线）



---

# < mark>

突出显示部分文本(默认为黄色背景)



---

# < sup>    < sub>

\<sup> 为上标

\<sub> 为下标



---

# < small>

显示效果：字体变小



---

### list-style-type

```html
<head>
    <style>
            ul.a {list-style-type: square;}
            ul.b {list-style-type: disc;}
            ul.c {list-style-type: circle;}
            ul.d {list-style-type: none;}
        </style>
</head>
<body>
    <ul class="a">
        <li>Coffee</li>		
        <li>Tea</li>
        <li>Me</li>
    </ul>
    <ul class="b">
        <li>Coffee</li>
        <li>Tea</li>
        <li>Me</li>
    </ul>
    <ul class="c">
        <li>Coffee</li>
        <li>Tea</li>
        <li>Me</li>
    </ul>
    <ul class="d">
        <li>Coffee</li>
        <li>Tea</li>
        <li>Me</li>
    </ul>
</body>
```



# 自定义列表标签

```
<head>
	<style>
        ul {
            list-style-image: url("img/turtle.png");
        }
    </style>
</head>
```



---

#  < dl>

< dl> 标签定义一个描述列表。

< dl> 标签与  < dt>（定义项目/名字）和 < dd>（描述每一个项目/名字）一起使用。

```html
  <dl>
        <dt>fishc.com.cn</dt>
        <dd>一个神奇的论坛。</dd>
    </dl>
```



---

# < table>

```html
<table>
    <tr>
        <th>姓名</th>
        <th>年龄</th>
    </tr>
    <tr>
        <td>小甲鱼</td>
        <td>18</td>
    </tr>
    <tr>
        <td>不二如是</td>
        <td>28</td>
    </tr>
</table>
```



# 边框线

```html
 <style>
        table {
            border: 1px solid black;
            border-collapse: collapse; 
        }
        th {
            border: 1px solid red;
        }
        td {
            border: 1px solid blue;
        }
    </style>
```

  border-collapse: collapse;    #为表格设置合并边框模型

---

# 标题< caption>

显示效果：给列表\<table>加一个标题



---

# 表格内边距

padding

```html
  <style>
        table {
            border: 1px solid black;
            border-collapse: collapse;
        }
        th, td {
            border: 1px solid black;
            padding: 5px;
        }
        caption {
            padding: 10px;
        }
    </style>
```



# 设置< th>元素的背景

```html
  <style>
        table {
            border: 1px solid black;
            border-collapse: collapse;
        }
        /* 将 th 元素的背景色设置为 grey */ 
        th {
            background: grey;
            color: white;
        }
        th, td {
            border: 1px solid black;
            padding: 5px;
        }
        caption {
            padding: 10px;
        }
    </style>
```



---

# < thead>   /  < tbody>   /  < tfoot>

使用方法：例

```html
 <table>
    <thead>
            <tr>
                <th>外号</th>
                <th>原名</th>
                <th>特长</th>
                <th>照片</th>
            </tr>
        </thead>
   </table>
```



# 跨行

```
<td rowspan="2">强化体格</td>
```

以上意思为跨两行



# 设置不同列的颜色

```html
  <table>
        <!-- 将第 1 列的背景颜色设置为红色 -->
        <!-- 将第 2、3 列的背景颜色设置为绿色 -->
        <colgroup>
            <col style="background: red">
            <col span="2" style="background: green">
        </colgroup>
```





---

# 表单

```html
<button type="submit">提交</button>
<button type="reset">重写</button>
```

```html
<button type="submit" formmethod="get">GET</button>
<button type="submit" formmethod="post">POST</button>
```

## \<label>

该标签不会显示样式，但是具有隐式关联作用

```html
<label for="name">你叫什么名字：</label><input type="text" name="name" id="name"><br><br>
<label>你叫什么名字：<input type="text" name="name"></label><br><br>
```



## \<fieldset>

将表单分组

```html
<body>
    <form action="welcome.php" method="post">
        <!-- 使用fieldset划分不同类型的表单元素 -->
        <fieldset>
            <label for="name">姓名：</label><input type="text" name="name" id="name"><br><br>
            <label for="sex">性别：</label><input type="text" name="sex" id="sex"><br><br>
            <label for="age">年龄：</label><input type="text" name="age" id="age">
        </fieldset>
        <p>为了给您提供更好的服务，希望您能抽出几分钟时间，将您的感受和建议告诉我们，我们非常重视每位用户的宝贵意见，期待您的参与！现在我们就马上开始吧！</p>
        <fieldset>
            <label for="q1">您是否使用过鱼C论坛：</label><input type="text" name="age" id="age"><br>
            <label for="q2">您使用鱼C论坛的目的是：</label><input type="text" name="age" id="age"><br>
            <label for="q3">您使用鱼C论坛的频率是：</label><input type="text" name="age" id="age">
        </fieldset>
        <br>
        <button type="submit">提交</button>
    </form>
</body>
```



 ## \<legend>

分组命名

```html
  <fieldset>
            <legend>基本信息</legend>
            <label for="name">姓名：</label><input type="text" name="name" id="name"><br><br>
            <label for="sex">性别：</label><input type="text" name="sex" id="sex"><br><br>
            <label for="age">年龄：</label><input type="text" name="age" id="age">
 </fieldset>
```



## < select>   \<option>

建立选项

```html
 <label for="sex">性别：
     <select name="sex">
     	<option value="male">男</option>
     	<option value="female">女</option>
     </select>
  </label>
```



## \<optgroup>

选项分组

```html
 <optgroup label="《零基础入门学习Web开发》">
            <option value="h5017">第017讲</option>
            <option value="h5016">第016讲</option>
            <option value="h5015">第015讲</option>
            <option value="h5000">……</option>
        </optgroup>
```



## \<input>

```html
<input type="submit" value="提交">
<input type="reset" value="重写">
<input type="button" onclick="msg()" value="点我！">
```



**单选框**

```html
<label><input type="radio" name="sex" value="male">男</label>
<label><input type="radio" name="sex" value="female">女</label>
```



**多选框**

```html
<p>你最喜欢的漫威英雄是？</p>
<input type="checkbox" name="heros[]" value="Deadpool">死侍
<input type="checkbox" name="heros[]" value="Venom">毒液
<input type="checkbox" name="heros[]" value="BlackWidow">黑寡妇
<input type="checkbox" name="heros[]" value="Hulk">绿巨人
<input type="checkbox" name="heros[]" value="GreenTurtle">小甲鱼
<input type="checkbox" name="heros[]" value="IronMan">钢铁侠
<input type="checkbox" name="heros[]" value="Wolverine">金刚狼
<input type="checkbox" name="heros[]" value="CaptainAmerica">美国队长
<br><br>
<input type="submit" value="提交">
```



**日期时间**

```html
<label>时间：<input type="time" name="time"></label>
<br><br>
<label>日期：<input type="date" name="date"></label>
<br><br>
<label>年月：<input type="month" name="month"></label>
<br><br>
<label>星期：<input type="week" name="week"></label>
<br><br>
<label>本地日期和时间：<input type="datetime-local" name="datetime-local"></label>
<br><br>
<input type="submit" value="提交">
```



 

```html
<input>实现搜索框
<input type="search" name="search">

<input>实现颜色选择框
<input type="color" name="color">

<input>实现图像按钮
<input type="image" src="turtle.png" alt="Green Turtle">

隐藏<input>元素
<input type="hidden" value="你看我不到" disabled></label>

<input>限制数字输入
<input type="number" name="age" min="1" max="100" step="1">

<inout>实现数字滚动条
<input type="range" name="love" value="1" min="1" max="10000" step="1">

<input>邮箱、手机、网址
邮箱：<input type="email" name="email">
手机：<input type="tel" name="tel"><
网址：<input type="url" name="url">

网址：<input type="url" name="url" pattern="^((https|http)?:\/\/)[^\s]+" placeholder="仅限以http和https开头的网址" required size="30" maxlength="22">
```



\<input>实现多文件上传，且规定上传文件格式

```html
<body>
<form action="upload.php" method="post" enctype="multipart/form-data">
<input type="hidden" name="MAX_FILE_SIZE" value="1024">
<label>请选择您要上传的文件：<input type="file" name="file" accept="image/*" multiple></label>
<br><br>
<input type="submit" value="提交">
</form>
</body>
```





```html
<body>
    <form action="welcome.php" method="post">
        <label>网址：<input type="url" name="url" pattern="^((https|http)?:\/\/)[^\s]+" placeholder="仅限以http和https开头的网址" required size="30" list="urllist"></label>
        <br><br>
        <input type="submit" value="提交">
    </form>
    <datalist id="urllist">
        <option value="https://ilovefishc.com">鱼C主页</option>
        <option value="https://fishc.com.cn">鱼C论坛</option>
        <option value="https://fishc.taobao.com">支持小甲鱼</option>
    </datalist>
</body>
```



---



# 文本框

```html
<textarea name="saysth" rows="5" cols="30" wrap="soft">123</textarea>
```



---



# \<div>

<div> 标签定义 HTML 文档中的一个分隔区块或者一个区域部分。


---

# 图像映射

```html
<body>
<img src="pic.jpg" alt="《零基础入门学习C语言》" usemap="#book">
<map name="book">
    <area shape="circle" coords="784,241,163" alt="Cup of coffee" href="https://fishc.com.cn" target="_blank">
    <area shape="poly" coords="279,230, 867,549, 636,975, 46,655" alt="Book" href="https://item.jd.com/12573534.html" target="_blank">
    <area shape="rect" coords="710,818,886,1008" alt="Turtle" href="https://man.ilovefishc.com" target="_blank">
</map>
</body>
```



----

# 图像适配

```html
<picture>
<source media="(min-width: 1024px)" srcset="big.jpg">
<source media="(min-width: 512px)" srcset="small.jpg">
<img src="normal.jpg" alt="小姐姐" style="width:auto;">
</picture>
```



---

# 插图

```html
<figure>
<img src="pic.jpg" alt="国际C语言混乱代码大赛">
<figcaption>国际C语言混乱代码大赛</figcaption>
</figure>
```



----

# video

```html
<video width="640" height="352" src="test_video.mp4" preload="metadata" controls></video>   
#阻止视频预加载
<video width="640" height="352" src="test_video.mp4" poster="pic.jpg" controls></video>
#为视频添加封面
<video id="FishC" src="http://fishc.oss-cn-hangzhou.aliyuncs.com/Web/video_tag.mp4" width="640" controls loops>非常抱歉，该浏览器不支持video</video>    
#循环播放
<video src="video.mp4" width="640" controls>
<track src="track.vtt" srclang="zh" label="中文字幕" kind="subtitles" default>
</video>   
#加字幕
<video width="640" controls>
<source src="ayok.mp4" type="video/mp4">
<source src="ayok.ogv" type="video/ogg">
<source src="ayok.webm" type="video/webm">
非常抱歉，本视频可能已经不在这个星球上了……
</video>
#备胎
```



----

# audio

```html
<audio src="1.mp3" controls>123</audio>
```





---

# 在网页中嵌入网页

```html
<iframe src="https://ilovefishc.com" width="1024px" height="800px">抱歉，您的浏览器不支持iframe。</iframe>

<iframe src="https://fishc.com.cn" width="1024px" height="800px" sandbox="allow-popups">抱歉，您的浏览器不支持iframe。</iframe>   #沙盒（sandbox)
```



---

# \<meter>

```html
<meter id="money" high="0.8" low="0.2" optimum="0.6" value="0.2" min="0" max="1"></meter>
```



---

# \<progress>显示进度条

```html
<progress max="1" value="0.8"></progress>

```

