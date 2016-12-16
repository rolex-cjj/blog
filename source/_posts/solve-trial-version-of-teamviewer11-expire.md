---
title: 解决TeamViewer 11 “试用期已到期”
date: 2016-09-28 12:53:15
tags: [TeamViewer]
---

远程控制，桌面共享，文件传输——TeamViewer，你值得拥有！

好吧，小小地打了下广告。最近在实验室因为某种原因需要windows远程连接ubuntu，我就选择了TeamViewer 11，它是跨平台的，确实挺好用，可是好端端的就提示我试用期已到期。神烦啊！

因为它是绑定了网卡的mac地址，所以仅卸载重新安装是无效的。看到网上有一些教程，试了一下，果然成功了！现在整理一下方便以后查看。我是先卸载原来的再进行以下步骤的，有人说不卸载完全退出就可以，大家可以试试。

<!--more-->

## 试用版变免费版

- 首先，在运行里输入%appdata%，在出来的列表中删除TeamViewer文件夹。

  ![](http://ohjnxvaxm.bkt.clouddn.com/teamviewer1.png)

- 在运行中输入regedit打开注册表，分别打开HKEY_LOCAL_MACHINE\SOFTWARE\ 和HKEY_CURRENT_USER\SOFTWARE\，删除该目录下的TeamViewer项。

  ![](http://ohjnxvaxm.bkt.clouddn.com/teamviewer2.png)

- 由于TeamViewer是与自己网卡的mac地址绑定，所以只能更改网卡mac地址。打开设备管理器，选择“网络适配器”中自己的有线网卡，右键选择“属性”。

- 选择“高级”->“Network Address”，选择“值”，原为不存在，任意输入12位数字。

  ![](http://ohjnxvaxm.bkt.clouddn.com/teamviewer3.png)

- 重新安装TeamViewer 11，安装时一定一定选择**个人/非商业用途**，打开惊喜地发现可以用啦！免费许可证！开心~~~

## 参考文献

[TeamViewer“试用期已到期”解决方法](http://blog.csdn.net/z249683156/article/details/41842271)

## 友情链接

[Qi's Blog ](https://nextinnovationucas.github.io/)