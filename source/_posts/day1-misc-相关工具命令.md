---
title: day1 misc(相关工具命令)
date: 2020-05-15 15:27:29
updated: 2020-05-15 15:27:29
categories: misc
---

misc第一天学习记录<!--more-->

## 文件后缀名判断

1.file命令

2.hex查看文件头



## 文件分离

### binwalk

分析文件：`binwalk filename`
分离文件：`binwalk -e filename`

遇到压缩包会自动解压

### foremost

分离文件：`foremost filename -o 输出文件名`

### dd

` dd if=源文件 of=目标文件名 bs=读写块的大写  count= 取的块数 skip=开始分离的字节数 `

<a href="<https://www.runoob.com/linux/linux-comm-dd.html>" target="_blank">linux dd命令</a>

### 十六进制编辑器手动分离

o10editor  winhex


## 文件合并

### linux

cat 合并的文件 >输出的文件

完整性检测

md5sum 文件名

### windows

copy /B 合并的文件(+) 输出的文件命令

完整性检测

certutil -hashfile 文件名 md5



## 图片隐写

### 1.细微的颜色差别

### 2.GIF图多帧隐藏

​	a.颜色通道隐藏
​        b.不同帧图信息隐藏
​        c.不同帧对比隐写

### 3.exif信息隐藏

### 4.图片修复

​     a.图片头修复
​     b.图片尾修复
​     c.CRC校验
​     d.长宽高修复
​     

### 5.最低有效位LSB隐写

### 6.图片加密

​     a.stegdelect
​     b.outguess
​     c.jphide
​     d.F5

### 7.工具

####  Firework

使用winhex打开文件看到文件头部包含firework标识，通过firework找到隐藏信息（类似PS）

#### Exif

图片右键 ->属性

linux下`$exiftool 文件名`

#### stegsolve

两种相同图片-->  Image combiner

lsb(最低有效位)  -->详见 图片文件隐写 -LSB  （文章下面）

#### TweakPNG

文件头正常却无法正常打开文件，tweakpng会弹出校验错误的提示（CRC）

再利用十六进制编辑器进行更改即可，若更改后还是无法正常打开，可退出为宽高被修改了

<a href="https://www.cnblogs.com/NPFS/p/12834774.html" target="_blank">宽高爆破脚本</a>

```
import struct
import binascii
import os

m = open("flag.png", "rb").read()
k = 0
for i in range(5000):
    if k == 1:
        break
    for j in range(5000):
        c = m[12:16] + struct.pack('>i', i) + struct.pack('>i', j) + m[24:29]
        crc = binascii.crc32(c) & 0xffffffff
        if crc == 0x889c2f07:
            k = 1
            print(hex(i), hex(j))
            break
```

```
#coding=utf-8
import zlib
import struct
#读文件
file = '1.png'  #注意，1.png图片要和脚本在同一个文件夹下哦~
fr = open(file,'rb').read()
data = bytearray(fr[12:29])
crc32key = eval(str(fr[29:33]).replace('\\x','').replace("b'",'0x').replace("'",''))
#crc32key = 0xCBD6DF8A #补上0x，copy hex value
#data = bytearray(b'\x49\x48\x44\x52\x00\x00\x01\xF4\x00\x00\x01\xF1\x08\x06\x00\x00\x00')  #hex下copy grep hex
n = 4095 #理论上0xffffffff,但考虑到屏幕实际，0x0fff就差不多了
for w in range(n):#高和宽一起爆破
    width = bytearray(struct.pack('>i', w))#q为8字节，i为4字节，h为2字节
    for h in range(n):
        height = bytearray(struct.pack('>i', h))
        for x in range(4):
            data[x+4] = width[x]
            data[x+8] = height[x]
            #print(data)
        crc32result = zlib.crc32(data)
        if crc32result == crc32key:
            print(width,height)
            #写文件
            newpic = bytearray(fr)
            for x in range(4):
                newpic[x+16] = width[x]
                newpic[x+20] = height[x]
            fw = open(file+'.png','wb')#保存副本
            fw.write(newpic)
            fw.close
```

#### Bftools(用于解密图片信息)

在windowsd的cmd下，对加密的图片进行解密

`Bftools.exe decode braincopter 要解密的图片名 -output 输出文件名`

`Bftools.exe run  上一步输出的文件`

#### SilenEye

用于将文字或文件隐藏到图片的解密工具

#### Stegdetect

用于探测JPG图像加密方式,例如 JSteg,JPHide,OutGuess,Fs等

`stegdetect xxx.jpg`

`stegdetect -s 敏感度 xxx.jpg`

>JPHide利用jphs解密
>OutGuess利用outguess工具解密，该工具需要编译使用./configure&&make&&make install 使用`outguess -r 文件名`
>
>F5利用F5工具解密，进入F5-stegdetect_F5目录，将图片复制到该目录，并CMD打开目录
>
>`Java Extract 解密文件  -p 密码`

#### QR(二维码处理)


##  图片文件隐写 -LSB

### 1.RGB

### 2.通过修改像素中最低位的1bit达到隐藏效果

### 3.工具 

#### stegsolve

打开文件>>Analyse>>Data Extract >>调整bit planes,bit order,bit plane order(多次尝试)>>preview

#### zsteg

适应PNG&BMP

installation `gem install zsteg`

method   `zsteg xxx.png` 

#### wbstego4(解密)

适应BMP&PDF&HTML&TXT

#### py脚本

method: `python LSB.py`

```
LSB.py
#coding:utf-8
import PIL.Image
def foo():
	im=PIL.Image.open('01.bmp')
	im2 = im.copy()
	pix = im2.load()
	width,heigth = im2.size
	
	for x in xrange(0,width):
		for y in xrange(0,height):
			if pix[x,y]&0x1 == 0:
				pix[x,y] = 0
			else
				pix[x,y]=255
		im2.show()
		pass
if __name__=='__main__':
	foo()
	print 'ok'
	pass
```





