---
title: Ubuntu下adb显示device not found及Permission denied解决办法
date: 2016-09-30 10:45:53
tags: [linux,adb,android]
---

明天就是国庆了，七天的假期啊！！宝宝也想回家。可是，回家，没有票；出去玩，没有票；男票，回家了！不开心！只能在实验室调bug，苦命的人啊。

最近接触了一点Android 开发，需要连接真机进行调试，会用到adb这个工具(Android Debug Bridge)(adb是一个 客户端-服务器端 程序, 其中客户端是你用来操作的电脑, 服务器端是android设备)。可是在Ubuntu12.04下连接设备以后，adb shell 总是提示device not found。有时候连上也会遇到Permission denied的问题，现将解决方法整理如下。

## device not found解决办法

1. 查看设备VID和PID

   使用lsusb查看usb设备，找到自己的设备

   <!--more-->

   ```bash
   lxb@lxb-Lenovo:~$ lsusb
   Bus 001 Device 002: ID 8087:8008 Intel Corp. 
   Bus 002 Device 002: ID 8087:8000 Intel Corp. 
   Bus 003 Device 017: ID 413c:2107 Dell Computer Corp. 
   Bus 003 Device 029: ID 1bbb:a100 T & A Mobile Phones 
   Bus 003 Device 003: ID 05e3:0610 Genesys Logic, Inc. 4-port hub
   Bus 003 Device 004: ID 17ef:6019 Lenovo 
   Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
   Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
   Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
   Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
   Bus 003 Device 005: ID 152d:2339 JMicron Technology Corp. / JMicron USA Technology Corp. JM20339 SATA Bridge
   ```

   我的是 Bus 003 Device 029: ID 1bbb:a100 T & A Mobile Phones ，其中1bbb是VID，后面的a100是PID，记住这两个值。

2. 修改adb_usb.ini文件，写入VID

   进入用户主目录，

   ```shell
   cd ~
   ls -al
   ```

   可以看到一个.android目录，进入该目录并找到adb_usb.ini文件，没有则创建之。写入VID。

   ```shell
   echo 0x1bbb> ~/.android/adb_usb.ini
   ```

3. 添加权限

   更改/etc/udev/rules.d/70-Android.rules。

   ```shell
   sudo gedit /etc/udev/rules.d/70-android.rules
   ```

   添加以下内容至该文件。

   ```shell
   SUBSYSTEM=="usb", ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="a100",MODE="0666"
   ```

   引号中两个ID就是刚才lsusb中获取的ID。

4. 更改权限

   ```shell
   sudo chmod a+rx /etc/udev/rules.d/70-android.rules
   ```

5. 重启USB服务

   ```shell
   sudo service udev restart
   ```

6. 重启adb服务

   ```shell
   lxb@lxb-Lenovo:~$ adb kill-server
   lxb@lxb-Lenovo:~$ adb start-server
   lxb@lxb-Lenovo:~$ adb devices
   List of devices attached 
   ec4f3e40	device
   ```

   adb devices有设备说明adb安装成功。这时候adb shell则可以正常显示。

   ```shell
   lxb@lxb-Lenovo:~$ adb shell
   shell@M812:/ $ 
   ```

## Permission denied解决方法

但是执行一些命令的时候发现会提示Permission denied。因为adb shell默认是没有root权限的，adb push一个文件就提示Permission Denied。删除system下的文件也没有权限。这里我介绍一种方法可以获取adb shell的root权限，但是前提是**手机已经root**。 

用su可以提权，直接执行su就会看到用户命令提示符由”$”变成了”#”，如果手机没有root，会提示su: Permission Denied。

```shell
shell@M812:/ $ su
root@M812:/ # 
```

此时就可以执行你的命令啦！

## 友情链接

[Qi's Blog ](https://nextinnovationucas.github.io/)

