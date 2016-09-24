---
title: Android Studio NDK 调试
date: 2016-09-24 10:05:28
tags: [Android,软件开发]
---

本人最近在接触Android Studio，是个新手，完全不懂，发现它和eclipse还是有一些差别的。Android开发过程中要Debug，弄了好久才弄好，现在整理一下，方便以后查看。

我的IDE是Android Studio 1.5.1，NDK是 android-ndk-r10e。前面的一些配置我就不细说了，主要是针对Debug整理一下。

## 步骤1

打开module的build.gradle文件，在buildTypes下添加以下代码。

```xml
debug{
  jniDebuggable true
}
```

<!--more-->

## 步骤2

同步build.gradle文件，选择Run->Edit configurations，选择自己的app native

然后就可以在C/C++代码中设置断点了。最后Run->Debug, OK。

## Note:

新版的Android Studio默认run configuration支持native debugging, 所以步骤2可以省去。

但是我在Debug时遇到了以下问题：

```
/usr/lib/x86_64-linux-gnu/libstdc++.so.6: version `GLIBCXX_3.4.19' not found
```

这是因为，当前程序的编译器的版本是比较低，只要升级一下就可以了。可以用如下命令查看一下当前GCC版本。

```
strings /usr/lib/x86_64-linux-gnu/libstdc++.so.6 | grep GLIBCXX
```

在我机器上显示

```
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
```

所以需要升级GCC版本。执行如下命令。

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install libstdc++6-4.7-dev
```

升级完成，再查看GCC版本

```
strings /usr/lib/x86_64-linux-gnu/libstdc++.so.6 | grep GLIBCXX
```

显示如下：

```
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_3.4.20
GLIBCXX_3.4.21
GLIBCXX_3.4.22
GLIBCXX_DEBUG_MESSAGE_LENGTH
```

这时就可以尽情地Debug 啦~开心~~~

## 友情链接

[Qi's Blog ](https://nextinnovationucas.github.io/)