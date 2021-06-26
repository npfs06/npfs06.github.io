---
title: JavaScript
date: 2020-04-05 22:43:54
updated: 2020-04-05 22:43:54
categories: 前端
---

以前学js时做的笔记,其实也没写什么<!--more-->

#### DOMO事件处理程序

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="div">
        <button type="submit" id="btn">提交</button>
    </div>
    <script>
    var dtn = document.getElementById("btn").onclick =  function demo(){alert("DOMO级事件处理程序");}     //会被覆盖
 
    </script>
</body>
</html>
```

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="div">
        <button type="submit" id="btn">提交</button>
    </div>
    <script>
    var dtn = document.getElementById("btn").addEventListener("click",demo)
    var dtn = document.getElementById("btn").addEventListener("click",demo1)  //不会被覆盖
        function demo(){
            alert("DOMO2级事件处理程序");
        }
        function demo1(){
            alert("DOMO2事件处理程序");
        }
        document.getElementById("btn").removeEventListener("click",demo1)  //移去该事件处理程序
    </script>
</body>
</html>
```





#### 事件对象

在触发DOM事件的时候都会产生一个对象


```
事件对象event：

1.type:获取事件类型
2.target:获取事件目标
3.stopPropagation():阻止事件冒泡
4.preventDefault():阻止事件默认行为
```





#### DATE对象

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body onload="startTime()">
    <script type="text/javascript">
        function startTime(){
            var d = new Date();
            var h = d.getHours();
            var m = d.getMinutes();
            var s = d.getSeconds();
            var t = setTimeout(function(){
               startTime(); 
            }, 500);
            m = checktime(m);
            s = checktime(s);
            document.getElementById("asd").innerHTML=h+":"+m+":"+s;
        }  
        function checktime(i){
            if(i<10){
                i = "0"+i;
            }
            return i;

        }    
        </script>
        <div id="asd"></div>
</body>
</html>
```



#### MATH对象

```
例： Math.round(2.5)    //得到3  把数四舍五入为最接近的整数。
```



#### DOM对象控制HTML

​    1.方法：

​      getElementsByName()  获取name

​      getElementsByTagName()  获取元素

​      getAttribute()  获取元素属性

​      setAttribute()  设置元素属性

​      childNodes()  访问子节点

​      parentNode()  访问父节点

​      createElement()  创建元素节点

​      createTextNode()  创建文本节点

​      insertBefore()  插入节点

​      removeChild()  删除节点

​      offsetHeight()  网页尺寸

​      scrollHeight()  网页尺寸  

```javascript
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
	</head>
	<body>
		<p name="pn">Hello</p>
		<p name="pn">Hello</p>
		<p name="pn">Hello</p>
		<p name="pn">Hello</p>
		<a id="aid" title="得到了A标签的属性">Hello</a>
		<a id="aid2">aid2</a>
		<ul>
			<li>1</li>
			<li>2</li>
			<li>3</li>
		</ul>
		<div id="div">
			<p id="pid">div的p元素</p>
		</div>
		<script>
			function getName(){
				var count = document.getElementsByTagName("p");
				alert(count.length);
				var p = count[0];
				p.innerHTML = "World";
			}
			
			function getAttr(){
				var anode = document.getElementById("aid");
				var attr = anode.getAttribute("title");
				alert(attr);
			}
			
			function setAttr(){
				var anode = document.getElementById("aid2");
				anode.setAttribute("title","动态设置a的title属性");
				var attr = anode.getAttribute("title");
				alert(attr);
			}
			
			function getChildNode(){
				var childnode = document.getElementsByTagName("ul")[0].childNodes;
				alert(childnode.length);	
				alert(childnode[0].nodeType);
				alert(childnode[1].nodeType);
			}
			
			function getParentNode(){
				var div = document.getElementById("pid");
				alert(div.parentNode.nodeName);
			}
			
			function createNode(){
				var body = document.body;
				var input = document.createElement("input");
				input.type = "button";
				input.value = "按钮";
				body.appendChild(input);
			}
			
			function addNode(){
				var div = document.getElementById("div");
				var node = document.getElementById("pid");
				var newNode = document.createElement("p");
				newNode.innerHTML = "动态添加第一个p元素";
				div.insertBefore(newNode,node);
			}
			
			function removeNode(){
				var div = document.getElementById("div");
				var p = div.removeChild(div.childNodes[1]);
			}
			
			function getSize(){
				var width = document.body.offsetWidth||document.documentElement.offsetWidth;
				var height = document.body.offsetHeight||document.documentElement.offsetHeight;
				alert(width+","+height);
			}
			getSize();
		</script>
	</body>
</html>
```

