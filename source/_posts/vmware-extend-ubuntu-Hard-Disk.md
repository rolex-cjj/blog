---
title: 使用gparted 扩充vmware中linux硬盘容量
date: 2016-11-15 11:14:20
tags: [linux]
---

在vmware中使用linux，经常会遇到初始化硬盘容量不够的情况，所以学会扩容很重要。我使用gparted工具进行空间扩展。

### 虚拟机设置

首先选择虚拟机->设置->硬盘->扩展，输入要扩大的容量。此时只是扩大了虚拟机的容量，还没有扩大分区容量。

### 安装gparted

进入linux，安装gparted工具。

```shell
sudo apt-get install gparted
```

打开gparted，发现有一部分为unallocated，此时需要把unallocated加入到/dev/sda1中，才能使用扩展后的空间。

也可以下载gparted 的iso文件，把iso镜像插入ubuntu启动光驱，启动虚拟机时进入vmware bios设置，选择从CD启动，然后进入gparted分区界面。

详情可以查看以下链接。

[使用gparted工具扩充vmware UBUNTU虚拟机磁盘空间](http://www.linuxdiyf.com/linux/23533.html)

[vmware虚拟机的ubuntu系统下不重新分区而扩大磁盘容量  ](http://blog.163.com/squall_smile/blog/static/60349840201362885726180/)