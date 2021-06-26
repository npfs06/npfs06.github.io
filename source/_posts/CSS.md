---
title: CSS
date: 2020-04-05 22:43:28
updated: 2020-04-05 22:43:28
categories: 前端
---

以前学css时做的笔记,啥也没记..<!--more-->

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style type="text/css">
    body {
        background-image: url("1.jpg");
        background-size: cover;
        background-repeat: no-repeat;
        font-size: 20px;
    }
    p {
        margin-top: 2px;
        color:white;
    }
    textarea {
        margin:30px 5px 15px 240px;
        background-color:dimgray ;
        padding: 8px 0px 0px 8px;
        color: white;
        overflow: hidden;
        resize: none;
    }
    form {
        margin: 0px 0px 0px 260px;
        color: azure;
    }
    input {
        margin: 0px 0px 0px 20px;
        background-color:transparent;
        border: 1px solid;
        border-radius: 5px;
        color: lavender;
    }
    textarea::placeholder {
        color:rgba(255, 255, 255, 0.5);
        font-size: 12px;
        resize:unset;
    }
    </style>
</head>
<body>
   <p>hello world!</p>
   
   <textarea rows="15" cols="70" placeholder="恭喜你 来到第二关"></textarea> 
   <form action="1.php" method="POST">
       <input type="username" name="username" placeholder="value1">
       <input id="as" type="username" name="username1" placeholder="value2">
       <input type="submit" value="提交">
   </form>
</body>
</html>
```



