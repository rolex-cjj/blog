---
title: linux 常用命令
date: 2016-12-02 11:13:00
tags: [linux]
---

学习linux是一个很漫长的过程，需要不断地动手练习才能灵活使用它。

<!--骐宝宝爱你~~-->

### **基础指令**

date、cal、bc(计算器)

### **文件与目录管理**

touch、mkdir、cp(在本机进行拷贝不能跨服务器)、scp(scp传输是加密的,基于ssh登陆进行安全的远程文件拷贝,win下有pscp)、mv(相同目录下使用为重命名，不同目录下为剪切)、rm(rm -rf一定要少用!!!深受其害!!! ~~o(>_<)o ~~)、file(查看文件类型)、pwd(查看当前路径)、ls(ls -l -a -h)、whoami(目前身份)

### **链接命令**

ln(常用ln -s 建立软链接，硬链接不常用)

<!--more-->

### **文件命令查询**

locate、which、whereis、find

locate是根据数据库/var/lib/mlocate/查找文件，不用读取硬盘，速度快。which和whereis两者均根据执行文件的路径变量$PATH搜索命令的位置。对于shell内部命令如cd，是搜不到的。只能搜外部命令。whereis除了可以列出命令路径，还可以列出命令帮助文档的路径。find读取硬盘查找，功能强大，但是速度慢，不常用。

### **帮助命令**

man、--help、help、info、/usr/share/doc

man常用命令有man man , man -f man(=whatis), man -k man(=apropos)。help只能用来查找shell内部命令。info命令行模式的网页显示数据，内容较多，不常用。判断是否为shell内部命令可先用whereis或者type查看。/usr/share/doc下的数据主要是以软件包为主的说明文档。

### **关机重启**

shutdown(shutdown -h /-r now)、halt、poweroff、init 0、reboot、init 6

### **改变文件属性和权限**

chgrp、chown、chmod

### **查看文件内容**

cat(一次性显示，连接文件)、tac、more(向下翻页)、less(可上下翻页)、head(head -n number file)、tail、od(非纯文本文件)

### **压缩命令**

zip/unzip、gzip/gunzip(=gzip -d)、bzip2/bunzip2(bzip2 -d)、tar(-c -v -f  -t -x -j -z)

zip常用命令zip test.zip test和zip -r test.zip test(压缩目录)，压缩后源文件存在。gzip常用gzip test和gzip -r test，gzip -r test可以压缩目录及子目录下的文件，不能压缩目录本身，压缩后源文件消失。bzip2常用bzip test(不保留源文件)和bzip -k test(保留源文件)，不能压缩目录。tar为打包命令，tar -cvf test.tar test(c打包，v显示打包过程，f指定打包文件名)，tar -xvf test.tar(x解打包)，tar -zcvf  test.tar.gz test，tar -jcvf  test.tar.bz2 test(源文件部分可以写以空格分割的多个文件)，tar -jxvf  test.tar.bz2 -C /tmp(解压到指定文件夹)，tar -jxv -f test.tar.bz2 待解开档名(相对路径)(实现解打包单一文件)。

### **磁盘目录容量**

df(df - h)、du

### **磁盘分区、格式化、检验、挂载**

fdisk、parted、mkfs、fsck、mount、unmount、开机挂载(/etc/fstab文件)

### **备份命令**

dump、dd

### **文本编辑器**

nano、vi、vim

vim真是个好东西啊！一定要熟练运用，其Visual block(常用v, y, yy, d, dd, p)，多档案编辑(:n, :N, :files)，多窗口功能(:sp {filename}, [ctrl]+w+↓/↑/q)，字符串搜索与替换(:n1,n2s/word1/word2/g)非常强大，常用命令还有1G, G, :set nu, :r filename, :! command。

![](http://ohjnxvaxm.bkt.clouddn.com/vim-commands.jpg)

### **变量**

特殊符号(', ", \, $, 反单引号\`, \`指令\`,  \$(指令))、export、env、declare、变量内容删除替代(#, ##, %, %%, /, // )

![](http://ohjnxvaxm.bkt.clouddn.com/variable.png)

### **shell**

alias、history、source(.)、type

可通过修改~/.bash_aliases或~/.bashrc文件添加别名，永久生效；history通过读取~/.bash_history文件获取以前下过的指令。history常配合!下达指令，!!, !n, !command。type可以找到执行指令是外部指令还是内建指令。

### **数据流重定向**

\>、>>、 <、 <<、2>、2>>、;、&&、||、tee(双向重导向)、$?(前一个指令执行完毕的回传值，0为成功)

### **管线命令**

**撷取命令**

cut(同一行里面的数据进行分解-d、-f)、grep(取出有关键字的行grep -n)、sed(-n p)、awk(处理每一行字段内的数据 )

awk常用用法：awk 'condition{function}…' 如awk '{print \$1 "\t" \$3}'，awk关键字有NF、NR、FS、BEGIN、END

**排序命令**

sort、uniq(-c 进行计数，计数时要先进行sort)、wc(列出文本中有多少行，多少字符)

**双向重导向**

tee

**字符转换**

tr(删除文字或文字替换-d、-s)、col、join(两文档以数据相关性贴在一起)、paste(将两行贴在一起)、split(大档案分割为小档案)

**减号-**

在管线命令当中，常常会使用前一个指令的stdout 作为这次指令的stdin，该stdin和stdout 可以用减号 "-" 来替代。

### **格式化输出**

printf(%ns、%ni)

### **档案对比**

diff(以行为单位，新旧档案纯文本对比)、cmp(以字节为单位，可以比较二进制文件)、patch(档案补丁)

### **正则表达式**

**!** 使用正则表达式要注意编码问题，统一LANG

**基础版**

^、$、[]、[^]、.、\* (重复前一个0次或多次)、.*(常用组合，0个或多个任意字符)、\\\{m,n\\}(限定范围)

![](http://ohjnxvaxm.bkt.clouddn.com/regex.png)

**扩展版**

grep默认支持基础正则表达式，使用扩展版时使用grep -E或者egrep

![](http://ohjnxvaxm.bkt.clouddn.com/regular-advance.png)

### **正则表达式与通配符**

通配符 (wildcard) 代表的是 **bash** 操作接口的一个功能， 但正则表示法则是一种**字符串处理**的表示方式。在文本过滤工具里，都是用正则表达式，比如像awk，sed，等，是针对**文件内容**的；而通配符多用在**文件名**上，比如查找find，ls，cp，等等。

通配符中*代表的是 0 ~ 无限多个字符，正则表达式中表示重复前一个RE字符0到多次，不能单独使用。

![](http://ohjnxvaxm.bkt.clouddn.com/wildcard.png)

### **shell script**

source(=.在父程序bash中执行)、sh script(在子程序bash中执行)、test(配合$?、&&、||使用进行文件测试)、[]判断、#!/bin/bash、shift(参数偏移)、if…then…fi、case…in…esac、while do done、until do done、for do done、sh -x script.sh(debug)、

### **工作管理**

&(将工作丢到背景中去执行)、[ctrl]-z(将目前工作丢到背景中暂停)、jobs(观察目前背景工作状态)、fg(将背景工作拿到前景来处理)、bg(让工作在背景下的状态变成运作中)、kill(管理背景当中的工作)、killall、%(后面跟job number)、nohup+&(让在背景的工作在你注销后还能够继续执行)、crontab(例行性工作)

### **进程管理**

ps(静态 -l查看自己bash进程状态、aux查看系统所有进程数据)、top(动态查看进程变化)、

### **系统资源查看**

free(内存使用情况)、lsb_release -a(显示操作系统版本信息，Linux Standard Base缩写lsb，意为linux标准规范库)、uname -a(显示当前操作系统与内核信息)、netstat(跟踪网络)、/proc/*、last(显示登陆者登陆信息)

### **源码编译软件安装**

./configure、make、makefile、make check、make install、gcc(-o、-L/path、-I/usr/include)、ldd(程序的动态函式库解析)、dpkg、rpm(-ivh、-qa、-Va)、

![](http://ohjnxvaxm.bkt.clouddn.com/software-manage.png)

### 远程连接

ssh、scp(windows下有pscp)