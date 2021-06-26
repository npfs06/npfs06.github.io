---
title: overthewire(bandit)
date: 2020-05-13 15:22:33
updated: 2020-05-13 15:22:33
categories:
	- [linux]
	- [Write up]
---

https://overthewire.org/wargames/

The wargames offered by the OverTheWire community can help you to learn and practice security concepts in the form of fun-filled games.<!--more-->

### level 0

xshell 连接

> 主机 : bandit.labs.overthewire.org
> Port: 2220

### level 0 -> level 1

```bash
bandit0@bandit:~$ ls
readme
bandit0@bandit:~$ cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
```

下一关用户名 bandit1 密码 boJ9jbbUNNfktd78OOpsqOltutMc3MY1

### level 1 -> level 2

```
bandit1@bandit:~$ ls
-
bandit1@bandit:~$ pwd
/home/bandit1
bandit1@bandit:~$ cat /home/bandit1/- 
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```

> 利用绝对路径进行读取

### level 2 -> level 3

```bash
bandit2@bandit:~$ cat spaces\ in\ this\ filename
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```

>  利用`\`进行转义空格

```bash
bandit2@bandit:~$ cat "spaces in this filename"
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```

>  利用双引号进行读取



### level 3 -> level 4

```
bandit3@bandit:~$ ls
inhere
bandit3@bandit:~$ cd inhere 
bandit3@bandit:~/inhere$ ls
bandit3@bandit:~/inhere$ ls -a
.  ..   .hidden 
bandit3@bandit:~/inhere$ cat .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB

```

> 隐藏文件读取





### level 4 -> level 5

```
bandit4@bandit:~$ ls
inhere
bandit4@bandit:~$ cd inhere/
bandit4@bandit:~/inhere$ ls -a
.   -file00  -file02  -file04  -file06  -file08
..  -file01  -file03  -file05  -file07  -file09
bandit4@bandit:~/inhere$ pwd
/home/bandit4/inhere
bandit4@bandit:~/inhere$ file /home/bandit4/inhere/*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
bandit4@bandit:~/inhere$ cat ./-file07
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
bandit4@bandit:~/inhere$

```

>Linux file命令用于辨识文件类型。
>通过file指令，我们得以辨识该文件的类型。





### level 5 -> level 6

- human-readable
- 1033 bytes in size
- not executable

```
bandit5@bandit:~$ ls
inhere
bandit5@bandit:~$ cd inhere/
bandit5@bandit:~/inhere$ ls -a
.            maybehere02  maybehere06  maybehere10  maybehere14  maybehere18
..           maybehere03  maybehere07  maybehere11  maybehere15  maybehere19
            maybehere00  maybehere04  maybehere08  maybehere12  maybehere16
            maybehere01  maybehere05  maybehere09  maybehere13  maybehere17
bandit5@bandit:~/inhere$ find . -size 1033c
./maybehere07/.file2
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
DXjZPULLxYr17uwoI01bNLQbtFemEgo7

```

> find命令用来在指定目录下查找文件。任何位于参数之前的字符串都将被视为欲查找的目录名。如果使用该命令时，不设置任何参数，则find命令将在当前目录下查找子目录与文件。并且将查找到的子目录和文件全部进行显示。
>
> 常用参数:
>
> -size n : 文件大小 是 n 单位，b 代表 512 位元组的区块，c 表示字元数，k 表示 kilo bytes，w 是二个位元组。
> -type c : 文件类型是 c 的文件。
> -name 文件名；
> -group 文件所属组；
> -perm权限(like 770)；



### level 5 -> level 6

- owned by user bandit7
- owned by group bandit6
- 33 bytes in size

```
bandit6@bandit:~$ find / -size 33c -user bandit7 -group bandit6 2>/dev/null
/var/lib/dpkg/info/bandit7.password
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```

>Linux系统预留可三个文件描述符：0、1和2，他们的意义如下所示：
>0——标准输入（stdin）
>1——标准输出（stdout）
>2——标准错误（stderr）
>
>重定向的符号有两个：>或>>，两者的区别是：前者会先清空文件，然后再写入内容，后者会将重定向的内容追加到现有文件的尾部。
>
>/dev/null是一个特殊的设备文件，这个文件接收到任何数据都会被丢弃。因此，null这个设备通常也被称为位桶（bit bucket）或黑洞。
>
>2>/dev/null的意思就是将标准错误stderr删掉