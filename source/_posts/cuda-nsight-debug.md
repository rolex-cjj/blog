---
title: linux下如何进行cuda debug
date: 2016-11-29 16:57:37
tags: [cuda,linux,nsight]
---

编代码容易，debug难啊！debug cuda程序并没有想象中那么容易。占用了我好久时间才弄好，还是太年轻啊！

我的环境是ubuntu16.04， cuda_toolkit 8.0 ，显卡驱动为nvidia-367，debug提示：

```
All CUDA devices are used for X11 and cannot be used while debugging
```

查阅[CUDA-GDB](http://docs.nvidia.com/cuda/cuda-gdb/index.html#using-debugger)发现：

Debugging a CUDA GPU involves pausing that GPU. When the graphics desktop manager is running on the same GPU, then debugging that GPU freezes the GUI and makes the desktop unusable。

#### **解决办法**

如果不用图形界面的话，可以直接停止X11 server通过停止lightdm服务；

如果要用图形界面的话，需要使用**cuda software preeption**.

<!--more-->

- 一种方法cuda-gdb 中使用以下命令，不过这种需要每次cuda-gdb都输入，比较麻烦。

```shell
set cuda software_preemption on
```

- 另一种方法设置环境变量，具体为设置~/.bashrc文件，最下方加入以下命令。不过这种支持设备SM3.5 compute capability 

```shell
export CUDA_DEBUGGER_SOFTWARE_PREEMPTION=1
```

在无意中发现，其实还有一种方法，使用IDE为Nsight Sclipse Edition,选择 window->preferences->Nsight,其中有一个选项Enable CUDA software preemption debugging,，只要勾选即可。再次Debug，没有问题啦~

![](http://ohjnxvaxm.bkt.clouddn.com/cuda-debug.png)



## 参考文献

[CUDA-GDB](http://docs.nvidia.com/cuda/cuda-gdb/index.html#axzz4Rf6hshHG)

## 友情链接

[Qi's Blog ](https://nextinnovationucas.github.io/)