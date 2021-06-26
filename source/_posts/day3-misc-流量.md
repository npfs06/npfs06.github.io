---
title: day3 misc(流量)
date: 2020-05-16 15:27:54
updated: 2020-05-16 15:27:54
categories: misc
---

在CTF比赛中，对于流量包的分析取证是一种十分重要的题型。通常这类题目都是会提供一个包含流量数据的pcap文件<!--more-->

# WireShark

## 常用过滤命令：

**过滤IP，如源IP或者目标x.x.x.x**

```
ip.src eq x.x.x.x or ip.dst eq x.x.x.x
或者
ip.addr eq x.x.x.x 
```

也可以：点击任意一个符合筛选条件的数据包，找到IPv4下的Destination字		  段，右键点击Source字段，作为过滤器应用 – 选中。

**过滤端口**

```
tcp.port eq 80 // 不管端口是来源的还是目标的都显示
tcp.port eq 80 or udp.port eq 80
tcp.dstport == 80 // 只显tcp协议的目标端口80
tcp.srcport == 80 // 只显tcp协议的来源端口80
过滤端口范围
tcp.port >= 1 and tcp.port <= 80
```

**过滤协议**

```
tcp/udp/arp/icmp/http/smtp/ftp/dns/msnms/ip/ssl......

排除tcp包，如!tcp   或者   not tcp
```

**http模式过滤**

```
http.request.method == "GET"
http.request.method == "POST"
http.request.uri == "/img/logo-edu.gif"
http contains "GET"
http contains "HTTP/1."

// GET包
http.request.method == "GET" && http contains "Host: "
http.request.method == "GET" && http contains "User-Agent: "

// POST包
http.request.method == "POST" && http contains "Host: "
http.request.method == "POST" && http contains "User-Agent: "

// 响应包
http contains "HTTP/1.1 200 OK" && http contains "Content-Type: "
http contains "HTTP/1.0 200 OK" && http contains "Content-Type: "
一定包含如下
`Content-Type:`
```


其他过滤命令参见<a href="https://blog.csdn.net/wojiaopanpan/article/details/69944970" target="_blank">wireshark过滤规则及使用方法</a>

## 协议分级

可以查看当前数据包中包含那些协议

> 统计 -> 协议分级

根据数据包特征进行筛选：右键->作为过滤器应用 ->选中/非选中

## 数据流跟踪

在关注的http数据包或tcp数据包中选择流汇聚，可以将HTTP流或TCP流汇聚或还原成数据，在弹出的框中可以看到数据内容。

> 选中数据分组后   右键 ->追踪流 ->TCP/HTTP流

HTTP流常见关键内容：

1.打开即可看到flag

2.上传/下载文件内容，内容包括文件名、hash值等

3.一句话木马

而TCP流通常是命令行操作

## 数据提取

使用wireshark可以自动提取通过http传输的文件内容，方法如下：

> 文件->导出对象->HTTP

在打开的对象列表中找到有价值的文件，如压缩文件、文本文件、音频文件、图片等，点击save进行保存，或者saveall保存所有对象再进入文件夹进行分析。

> media type ->右键->导出分组字节流

> 文件->导出分组字节流

> 选中http文件传输流量包，在分组详情中找到data或者Line-based text data:text/html层，鼠标右键点击 – 选中 导出分组字节流。



# 无线wifi流量包

协议分级发现只有wireless LAN协议，很可能是WPA或者WEP加密的无线数据包

**aircrack-ng**

Linux下用aircrack-ng检查cap包

`aircrack-ng  xxx.cap -w 字典`



# USB流量包分析

USB流量指的是USB设备接口的流量，攻击者能够通过监听usb接口流量获取键盘敲击键、鼠标移动与点击、存储设备的铭文传输通信、USB无线网卡网络传输内容等等。在CTF中，USB流量分析主要以键盘和鼠标流量为主。

## 键盘流量

USB协议数据部分在Leftover Capture Data域中，数据长度为`八个字节`。其中键盘击键信息集中在第三个字节中。
![](https://img.npfs06.top/20210325160246.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

如图，发现击键信息为0x06，即对应的按键为`C`
键位映射关系参考：[《USB键盘协议中键码》中的HID Usage ID](https://wenku.baidu.com/view/9050c3c3af45b307e971971e.html)

### 题型：

flag隐藏在usb流量中，通过USB协议数据中的键盘键码转换成键位。

### 解题思路：

1.使用kali linux中的tshark 命令把cap data提取出来：

```shell
tshark -r usb.pcap -T fields -e usb.capdata > usbdata.txt
tshark -r usb.pcap -T fields -e usb.capdata | sed '/^\s*$/d' > usbdata.txt #提取并去除空行
```

2.根据《USB键盘协议中键码》中的HID Usage ID将数据还原成键位，可写一个Python脚本进行快速转换。

### 题目示例：

[【NSCTF】安全评测人员在对某银行卡密码输入系统进行渗透测试，截获了一段通过USB键盘输入6位数字密码的流量，其中也包含了一些其他无关的USB设备的流量，你能从中恢复出6位数字密码吗？最终提交的flag格式为flag](https://pan.baidu.com/s/1bGEIPeXDCbhybmWOyGr8Og)
提取码：q6ro
（1）使用tshark 命令把pcap的数据提取并去除空行到`usbdata.txt`

```shell
tshark -r usb.pcap -T fields -e usb.capdata | sed '/^\s*$/d' > usbdata.txt
```

![](https://img.npfs06.top/20210325160311.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
（2）提取出来的数据可能会带冒号，也可能不带（有可能和wireshark的版本相关），但是一般的脚本都会按照有冒号的数据来识别

> 有冒号时提取数据的`[6:8]`
> 无冒号时数据在`[4:6]`

可以用脚本来加上冒号

```py
f=open('usbdata.txt','r')
fi=open('out.txt','w')
while 1:
    a=f.readline().strip()
    if a:
        if len(a)==16: # 鼠标流量的话len改为8
            out=''
            for i in range(0,len(a),2):
                if i+2 != len(a):
                    out+=a[i]+a[i+1]+":"
                else:
                    out+=a[i]+a[i+1]
            fi.write(out)
            fi.write('\n')
    else:
        break

fi.close()
```

此时对应的第三字节，也就是[6:8]就代表了击键信息
（3）提取出键盘流量后需要用脚本还原数据对应的信息。同时找到两个还原信息的脚本（python2）：
`keyboard1.py`

```py
mappings = { 0x04:"A",  0x05:"B",  0x06:"C", 0x07:"D", 0x08:"E", 0x09:"F", 0x0A:"G",  0x0B:"H", 0x0C:"I",  0x0D:"J", 0x0E:"K", 0x0F:"L", 0x10:"M", 0x11:"N",0x12:"O",  0x13:"P", 0x14:"Q", 0x15:"R", 0x16:"S", 0x17:"T", 0x18:"U",0x19:"V", 0x1A:"W", 0x1B:"X", 0x1C:"Y", 0x1D:"Z", 0x1E:"1", 0x1F:"2", 0x20:"3", 0x21:"4", 0x22:"5",  0x23:"6", 0x24:"7", 0x25:"8", 0x26:"9", 0x27:"0", 0x28:"\n", 0x2a:"[DEL]",  0X2B:"    ", 0x2C:" ",  0x2D:"-", 0x2E:"=", 0x2F:"[",  0x30:"]",  0x31:"\\", 0x32:"~", 0x33:";",  0x34:"'", 0x36:",",  0x37:"." }

nums = []
keys = open('out.txt')
for line in keys:
    if line[0]!='0' or line[1]!='0' or line[3]!='0' or line[4]!='0' or line[9]!='0' or line[10]!='0' or line[12]!='0' or line[13]!='0' or line[15]!='0' or line[16]!='0' or line[18]!='0' or line[19]!='0' or line[21]!='0' or line[22]!='0':
         continue
    nums.append(int(line[6:8],16))

keys.close()

output = ""
for n in nums:
    if n == 0 :
        continue
    if n in mappings:
        output += mappings[n]
    else:
        output += '[unknown]'

print 'output :\n' + output
keyboard2.py
normalKeys = {
    "04":"a", "05":"b", "06":"c", "07":"d", "08":"e",
    "09":"f", "0a":"g", "0b":"h", "0c":"i", "0d":"j",
     "0e":"k", "0f":"l", "10":"m", "11":"n", "12":"o",
      "13":"p", "14":"q", "15":"r", "16":"s", "17":"t",
       "18":"u", "19":"v", "1a":"w", "1b":"x", "1c":"y",
        "1d":"z","1e":"1", "1f":"2", "20":"3", "21":"4",
         "22":"5", "23":"6","24":"7","25":"8","26":"9",
         "27":"0","28":"<RET>","29":"<ESC>","2a":"<DEL>", "2b":"\t",
         "2c":"<SPACE>","2d":"-","2e":"=","2f":"[","30":"]","31":"\\",
         "32":"<NON>","33":";","34":"'","35":"<GA>","36":",","37":".",
         "38":"/","39":"<CAP>","3a":"<F1>","3b":"<F2>", "3c":"<F3>","3d":"<F4>",
         "3e":"<F5>","3f":"<F6>","40":"<F7>","41":"<F8>","42":"<F9>","43":"<F10>",
         "44":"<F11>","45":"<F12>"}
shiftKeys = {
    "04":"A", "05":"B", "06":"C", "07":"D", "08":"E",
     "09":"F", "0a":"G", "0b":"H", "0c":"I", "0d":"J",
      "0e":"K", "0f":"L", "10":"M", "11":"N", "12":"O",
       "13":"P", "14":"Q", "15":"R", "16":"S", "17":"T",
        "18":"U", "19":"V", "1a":"W", "1b":"X", "1c":"Y",
         "1d":"Z","1e":"!", "1f":"@", "20":"#", "21":"$",
          "22":"%", "23":"^","24":"&","25":"*","26":"(","27":")",
          "28":"<RET>","29":"<ESC>","2a":"<DEL>", "2b":"\t","2c":"<SPACE>",
          "2d":"_","2e":"+","2f":"{","30":"}","31":"|","32":"<NON>","33":"\"",
          "34":":","35":"<GA>","36":"<","37":">","38":"?","39":"<CAP>","3a":"<F1>",
          "3b":"<F2>", "3c":"<F3>","3d":"<F4>","3e":"<F5>","3f":"<F6>","40":"<F7>",
          "41":"<F8>","42":"<F9>","43":"<F10>","44":"<F11>","45":"<F12>"}
output = []
keys = open('out.txt')
for line in keys:
    try:
        if line[0]!='0' or (line[1]!='0' and line[1]!='2') or line[3]!='0' or line[4]!='0' or line[9]!='0' or line[10]!='0' or line[12]!='0' or line[13]!='0' or line[15]!='0' or line[16]!='0' or line[18]!='0' or line[19]!='0' or line[21]!='0' or line[22]!='0' or line[6:8]=="00":
             continue
        if line[6:8] in normalKeys.keys():
            output += [[normalKeys[line[6:8]]],[shiftKeys[line[6:8]]]][line[1]=='2']
        else:
            output += ['[unknown]']
    except:
        pass

keys.close()

flag=0
print("".join(output))
for i in range(len(output)):
    try:
        a=output.index('<DEL>')
        del output[a]
        del output[a-1]
    except:
        pass

for i in range(len(output)):
    try:
        if output[i]=="<CAP>":
            flag+=1
            output.pop(i)
            if flag==2:
                flag=0
        if flag!=0:
            output[i]=output[i].upper()
    except:
        pass

print ('output :' + "".join(output))
```

运行第一个脚本得到
![](https://img.npfs06.top/20210325160328.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)

```shell
BCFGIJGFEDCABACFEDCA7200[DEL]53[DEL]93
因为[DEL]是删除键,恢复出6位数字。所以flag: 720593
```

## 鼠标流量

USB协议鼠标数据部分在Leftover Capture Data域中，数据长度为`四个字节`。

> 其中第一个字节代表按键，当取0x00时，代表没有按键、为0x01时，代表按左键，为0x02时，代表当前按键为右键。
> 第二个字节可以看成是一个signed byte类型，其最高位为符号位，当这个值为正时，代表鼠标水平右移多少像素，为负时，代表水平左移多少像素。
> 第三个字节与第二字节类似，代表垂直上下移动的偏移。

![](https://img.npfs06.top/20210325160345.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
如图，数据信息为0x00002000，表示鼠标垂直向上移动20。

### 题型：

flag隐藏在usb流量中，通过USB协议数据中的鼠标移动轨迹转换成flag。

### 解题思路：

1.使用kali linux中的tshark 命令把cap data提取出来，并去除空行

```shell
tshark -r usb2.pcap -T fields -e usb.capdata > usbdata.txt
tshark -r usb2.pcap -T fields -e usb.capdata | sed '/^\s*$/d' > usbdata.txt #提取并去除空行
```

2.根据usb协议鼠标数据还原鼠标移动轨迹，可写一个Python脚本进行快速还原。

### 题目示例：

[【NSCTF】这是一道鼠标流量分析题。](https://pan.baidu.com/s/1bGEIPeXDCbhybmWOyGr8Og)
提取码：q6ro
（1）使用tshark 命令把pcap的数据提取并去除空行到`usbdata.txt`

```shell
tshark -r usb2.pcap -T fields -e usb.capdata | sed '/^\s*$/d' > usbdata.txt
```

（2）使用上面提到过的加冒号的脚本，并将脚本里提到的`16`改为`8`，得到

```py
python3 maohao.py
```

![](https://img.npfs06.top/20210325160403.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
（3）使用mouse.py测试信息隐藏位置

```py
nums = []
keys = open('out.txt','r')
f = open('xy.txt','w')
posx = 0
posy = 0
for line in keys:
    if len(line) != 12 :
        continue
    x = int(line[3:5],16)
    y = int(line[6:8],16)
    if x > 127 :
        x -= 256
    if y > 127 :
        y -= 256
    posx += x
    posy += y
    btn_flag = int(line[0:2],16)  # 1 for left , 2 for right , 0 for nothing
    if btn_flag == 2 : # 1 代表左键
        f.write(str(posx))
        f.write(' ')
        f.write(str(posy))
        f.write('\n')

f.close()
```

测试发现flag信息藏在右键中，即当脚本中btn_flag取2时可以得到一系列坐标
![](https://img.npfs06.top/20210325160420.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
（4）用gnuplot将`xy.txt`里的坐标转化成图像

```shell
gnuplot
gnuplot>plot "xy.txt"
```

![](http://img.npfs06.top/20210325160435.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)发现方向反了，使用windows上的"画图"垂直翻转一下即可。
![](http://img.npfs06.top/20210325160454.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)
最终得到flag
![](http://img.npfs06.top/20210325160505.png?imageView2/0/q/75|watermark/2/text/bnBmczA2LnRvcA==/font/5b6u6L2v6ZuF6buR/fontsize/340/fill/IzAwMDAwMA==/dissolve/62/gravity/SouthEast/dx/10/dy/10)



参考 <a href="https://blog.csdn.net/qq_43625917/article/details/107723635" target ="_blank" >https://blog.csdn.net/qq_43625917/article/details/107723635</a>

