---
title: CTF压缩包分析
date: 2020-04-11 10:16:53
updated: 2020-04-11 10:16:53
categories: misc
---

CTF中常见的压缩包题型总结

<!--more-->

# 暴力破解

 利用 ARCHPR  



# 明文攻击

明文攻击指的是有一个加密的ZIP和一个未加密的压缩包中包含的文件，由于同一个ZIP压缩包里的所有文件都是使用同一个加密密钥来加密的，所以可以使用已知文件推测出密钥并解密ZIP文件的攻击方式。

## 解题思路：

假设题目提供了一张图片和一个压缩包，发现压缩包加密，且压缩包中有一张图片和外面的图片命名是一样的，猜测为明文攻击。使用软件将图片进行压缩后发现，压缩包中的图片和压缩后的图片的CRC32值是一样的，确认为明文攻击。

## 做法：

打开ARCHPR，打开待解密的压缩包，选择“攻击类型”为“明文”，点击“明文”选项选择刚才将图片压缩的压缩包。点击“开始”后就开始进行破解，得到密钥。

【注：不同的压缩软件使用的压缩算法不同，自行压缩的那个压缩包需要同题目给的压缩包的压缩算法一直才能进行明文攻击（常见压缩软件：好压、winRAR、7z、快压、360压缩）】

# ARCHPR掩码

选择攻击类型为掩码，abc???,abc为已知字符，???代表未知字符（用三个问号表示）

# CRC32碰撞(适合字节小的压缩包文件)

CRC的全称是循环冗余校验(Cyclic Redundancy Check, CRC)，不同长度的常数对应着不同的CRC实现算法，可用来校验文件是否出错但并不能用于自动纠错。

## 解题思路：

题目解压打开发现有多个压缩包可尝试CRC32碰撞

## 做法：

编写脚本，尝试使用CRC32碰撞还原出所有压缩包中文件的内容

```
参考大神写的脚本
import zipfile
import string
import binascii
 
def CrackCrc(crc):
    for i in dic:
        for j in dic:
            for p in dic:
                #for q in dic:
                    s = i + j + p
                    if crc == (binascii.crc32(s) & 0xffffffff):
#在 Python 2.x 的版本中，binascii.crc32 所计算出來的 CRC 值域为[-2^31, 2^31-1] 之间的有符号整数，为了要与一般CRC结果作比对，需要将其转为无符号整数，所以加上& 0xffffffff来进行转换。如果是 Python 3.x 的版本，其计算结果为 [0, 2^32-1] 间的无符号整数，因此不需额外加上& 0xffffffff
                        #print s
                        f.write(s)	
                        return

def CrackZip():
    for I in range(36):
        file = 'flag' + str(I) + '.zip'
        f = zipfile.ZipFile(file, 'r')
        GetCrc = f.getinfo('flag.txt')
        crc = GetCrc.CRC
        #以上3行为获取压缩包CRC32值的步骤
        #print hex(crc)
        CrackCrc(crc)
 
dic = string.ascii_letters + string.digits + '+/='
 
f = open('out.txt', 'w')
CrackZip()
f.close()

```

如果压缩包文件为6字节

可利用工具[https://github.com/theonlypwner/crc32 ](https://github.com/theonlypwner/crc32 )

使用方法：
`python crc32.py reverse crc32密文(16进制形式)`



# zip伪加密

> 组成：压缩源文件数据区+压缩源文件目录区+压缩源文件目录结束标志
> ZIP的文件头标识固定为0×50 4B 03 04
> 核心目录区的标记为0x 50 4B 01 02
> 核心目录结束标记0×50 4B 05 06 



## 解题思路

查看压缩源文件目录区的全布局方式标记

```
50 4B 01 02 3F 00 14 00 09 00
第9、10位00 00，即为全布局方式标记，由此判断有无加密（伪加密的关键）
【注：全局方式位标记的四个数字中只有第二个数字对其有影响，其它的不管为何值，都不影响它的加密属性！】 
第二个数字为奇数时 –>加密 
第二个数字为偶数时 –>未加密
```

## 做法

将全局方式位标记改为00 00



# 图片中隐藏压缩包

binwalk或foremost分离

以jpg文件为例，jpg文件以FF D9结尾，而图片浏览器会忽略FF D9后面的内容，此次可在其后面加入其它文件

针对此类题目，可将图片拖进十六进制编辑器，观察文件格式

或者直接拉进kali binwalk/foremost  扫描分离