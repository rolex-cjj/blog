---
title: linux 常用命令
date: 2016-11-16 11:13:00
tags: [linux]
---

学习linux是一个很漫长的过程，需要不断地动手练习才能灵活使用它。

骐宝宝爱你~~

#### 目录处理命令

mkdir、cp、mv(相同目录下使用为重命名，不同目录下为剪切)、rm(常用rm -rf)

#### 链接命令

ln(常用ln -s 建立软链接，硬链接不常用)

#### 文件搜索命令

locate

根据数据库/var/lib/mlocate/查找文件，速度快。

#### 命令搜索命令

which、whereis

两者均根据执行文件的路径变量$PATH搜索命令的位置。对于shell内部命令如cd，是搜不到的。只能搜外部命令。wheris除了可以列出命令路径，还可以列出命令帮助文档的路径。

#### find命令

速度较慢，但是功能强大。

#### grep命令

查找指定文件中的含有特定字符串的一行。

#### 帮助命令

man、--help、help、info、/usr/share/doc

man常用命令有man man , man -f man(=whatis), man -k man(=apropos)。help只能用来查找shell内部命令。info命令行模式的网页显示数据，内容较多，不常用。判断是否为shell内部命令可先用whereis查看，不能列出命令位置的为shell内部命令。/usr/share/doc下的数据主要是以软件包为主的说明文档。

#### 文本编辑器

nano、vi、vim

#### 关机重启命令

shutdown(shutdown -h /-r now)、halt、poweroff、init 0、reboot、init 6

改变文件属性和权限

chgrp、chown、chmod



pwd、lsb_release -a、

#### 