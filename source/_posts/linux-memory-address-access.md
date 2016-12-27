---
title: linux下内存权限管理及进程地址空间查看
date: 2016-09-30 11:41:36
tags: [linux]
---

最近在linux下做开发，需要修改内存地址的值，这就涉及到内存权限的问题。这里主要讲两个方面的东西，一是修改内存权限，另一个是进程地址空间之初探。

## 内存权限

在Linux中，mprotect()函数可以用来修改一段指定内存区域的保护属性。

函数原型如下：
```c
#include <unistd.h>
#include <sys/mmap.h>
int mprotect(const void *start, size_t len, int prot);
```

mprotect()函数把自start开始的、长度为len的内存区的保护属性修改为prot指定的值。

<!--more-->

prot可以取以下几个值，并且可以用“|”将几个属性合起来使用：
1）PROT_READ：表示内存段内的内容可写；
2）PROT_WRITE：表示内存段内的内容可读；
3）PROT_EXEC：表示内存段中的内容可执行；
4）PROT_NONE：表示内存段中的内容根本没法访问。
需要指出的是，所指定的内存区间必须包含整个内存页（4K）。区间开始的地址start必须是一个**内存页的起始地址**，并且区间长度len必须是**页大小的整数倍**。

如果执行成功，则返回0；如果执行失败，则返回-1，并且设置errno变量，说明具体因为什么原因造成调用失败。错误的原因主要有以下几个：
1）EACCES
该内存不能设置为相应权限。这是可能发生的，比如，如果你 mmap(2) 映射一个文件为只读的，接着使用 mprotect() 标志为 PROT_WRITE。
2）EINVAL
start 不是一个有效的指针，指向的不是某个内存页的开头。
3）ENOMEM
内核内部的结构体无法分配。
4）ENOMEM
进程的地址空间在区间 [start, start+len] 范围内是无效，或者有一个或多个内存页没有映射。

如果调用进程内存访问行为侵犯了这些设置的保护属性，内核会为该进程产生 SIGSEGV （Segmentation fault，段错误）信号，并且终止该进程。

## linux进程空间地址

Linux进程的虚拟内存区域一般有：代码段、数据段、堆、用户栈、共享段。每个存在的虚拟页面都保存在某个区域中，而不属于某个区域的虚拟页是不存在的，并且不能被进程访问。内核不用记录那些不存在的虚拟页，而这样的页也不占用存储器、磁盘或者内核本身的其他任何资源。

 进程只能访问有效内存区域的内存地址，每个内存区域也具有相关权限，如可读、可写、可执行性质。如果一个进程访问了无效范围中的内存区域或者以不正确的方式访问了有效地址，那么内核就会终止该进程，并返回  “段错误”信息。

linux可以用/proc 文件系统和 pmap,ps工具查看给定进程的内存空间及所包含的内存区域。

1. pmap 命令

   ```shell
   pmap -d PID
   ```

   ```shell
   lxb@lxb-Lenovo:~$ pmap -d 10901
   10901:   /bin/sh /home/lxb/android_studio_environment/android-studio/bin/studio.sh
   Address           Kbytes Mode  Offset           Device    Mapping
   0000000000400000     104 r-x-- 0000000000000000 008:00005 dash
   0000000000619000       4 r---- 0000000000019000 008:00005 dash
   000000000061a000       4 rw--- 000000000001a000 008:00005 dash
   000000000061b000      12 rw--- 0000000000000000 000:00000   [ anon ]
   0000000001516000     132 rw--- 0000000000000000 000:00000   [ anon ]
   00007fb26a81e000    1744 r-x-- 0000000000000000 008:00005 libc-2.15.so
   00007fb26a9d2000    2044 ----- 00000000001b4000 008:00005 libc-2.15.so
   00007fb26abd1000      16 r---- 00000000001b3000 008:00005 libc-2.15.so
   00007fb26abd5000       8 rw--- 00000000001b7000 008:00005 libc-2.15.so
   00007fb26abd7000      20 rw--- 0000000000000000 000:00000   [ anon ]
   00007fb26abdc000     136 r-x-- 0000000000000000 008:00005 ld-2.15.so
   00007fb26addf000      12 rw--- 0000000000000000 000:00000   [ anon ]
   00007fb26adfc000       8 rw--- 0000000000000000 000:00000   [ anon ]
   ```

2. ps命令

   ```shell
   ps aux|grep process_name
   ```

   ```shell
   lxb@lxb-Lenovo:~$ ps aux|grep studio.sh
   lxb      10901  0.0  0.0   4400   696 pts/2    S+   09:09   0:00 /bin/sh\
   >/home/lxb/android_studio_environment/android-studio/bin/studio.sh
   lxb      15793  0.0  0.0  13612   988 pts/5    S+   12:59   0:00 grep --color=auto studio.sh
   ```

3. 查看/proc/PID/maps文件

   每行数据格式为：

   起始地址-尾部地址    访问权限    偏移   主设备号：次设备号   i节点   文件名

```shell
lxb@lxb-Lenovo:~$ cat /proc/10901/maps
00400000-0041a000 r-xp 00000000 08:05 25952284                           /bin/dash
00619000-0061a000 r--p 00019000 08:05 25952284                           /bin/dash
0061a000-0061b000 rw-p 0001a000 08:05 25952284                           /bin/dash
0061b000-0061e000 rw-p 00000000 00:00 0 
01516000-01537000 rw-p 00000000 00:00 0                                  [heap]
7fb26a81e000-7fb26a9d2000 r-xp 00000000 08:05 7087038                    /lib/x86_64-linux-gnu/libc-2.15.so
```

## 参考文献

[linux下查看进程内存使用情况](http://blog.csdn.net/sunlylorn/article/details/6215137)

[linux c之使用mprotect检测内存访问](http://www.linuxdiyf.com/linux/21096.html)

## 友情链接

[Qi's Blog ](https://nextinnovationucas.github.io/)