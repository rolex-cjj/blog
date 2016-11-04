---
title: Ubuntu16.04 使用apt-mirror配置本地源
date: 2016-10-18 15:40:17
tags: [ubuntu,apt-mirror]
---

### **安装apt-mirror**

```shell
$sudo apt-get install apt-mirror
```

- **查看系统的研发代号**

首先查看自己的Ubuntu系统的Codename，这直接导致你更新的源是否对你的系统起效果，查看方法：

```shell
lsb_release –a
```

我的系统Codename为xenial。

- **确认源支持**

以阿里源为例，登陆 http://mirrors.aliyun.com/ubuntu/dists/ ，该网页显示了阿里云支持的Ubuntu系统下各个Codename版本，确保自己的Codename在该网页中存在。

附上Ubuntu16.04源列表：http://wiki.ubuntu.com.cn/%E6%A8%A1%E6%9D%BF:16.04source

<!---more-->

### **配置apt-mirror**

```shell
$sudo vim /etc/apt/mirror.list
```

```shell
############# config##################
#set base_path    /var/spool/apt-mirror
# if you change the base path youmust create the directories below with write privlages
#set mirror_path  $base_path/mirror 
#set skel_path    $base_path/skel
#set var_path     $base_path/var
#set cleanscript$var_path/clean.sh
#set defaultarch  <running host architecture>
set nthreads     20
set _tilde 0
##要想更换镜像文件存放目录，只需修改以下语句,但事先要创建所需文件夹。这里采用默认位置。
##set base_path  /var/spool/apt-mirror
############ end config################
##在这里我们把常用的软件同步过来就够用了
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universemultiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universemultiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universemultiverse
deb-i386 http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb-i386 http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universemultiverse
deb-i386 http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universemultiverse
deb-i386 http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-i386 http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universemultiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universemultiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universemultiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universemultiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universemultiverse
##deb http://* 表示下载64位版本的deb软件。
##deb-i386 http://* 表示下载32位版本的deb软件。
#脚本来删除遗留在本地的且不需要用的软件包
clean http://mirrors.aliyun.com/ubuntu/
##当某些软件包在服务器端进行了升级，或者服务器端不再需要这些软件包时，我们使用了apt-mirror与服务器同步后
##会在本地的$var_path/下生成一个clean.sh的脚本，列出了遗留在本地的旧版本和无用的软件包，你可以手动运行这个
```



### **下载deb包**

```shell
$sudo apt-mirror
```

和指定的镜像进行同步，这会启动20个线程，运行wget到 http://mirrors.aliyun.com/ubuntu/ 下面下载相应的deb包。

同步完成后，我们可以利用clean.sh清理无用软件包。

```shell
$sudo sh /var/spool/apt-mirror/var/clean.sh
```

 

### **安装apache2**

需要启动一个web服务器来启动本地的Ubuntu Source服务器。选择使用apache2这个web服务器来为Ubuntu Source服务器服务。

```shell
$sudo apt-get install apache2
```

- **查看web根目录**

```shell
$sudo cat /etc/apache2/sites-enabled/000-default
```

这里使用var/www/目录作为Web的根目录。

- **创建一个软链接**

```shell
ln -s /var/spool/apt-mirror/mirror/mirrors.aliyun.com/ubuntu/ /var/www/ubuntu
```

或者 修改apache2配置文件

```shell
$sudo vim /etc/apache2/sites-enabled/000-default
```

将DocumentRoot /var/www改为：

```shell
DocumentRoot /var/spool/apt-mirror/mirror/mirrors.aliyun.com
```

 我这里采用了创建软链接的方法。

- **启动服务器**

```shell
sudo /etc/init.d/apache2 start
```

使用以下命令可以管理apache2服务器。

```shell
sudo /etc/init.d/apache2 start/restart/stop/status
```

打开浏览器，输入 [http://localhost/ubuntu](http://localhost/ubuntu) ，就可以看到和 [http://mirrors.aliyun.com/ubuntu/](undefined) 相同的页面。

至此，我们的本地Ubuntu Source服务器已经创建成功了。

- **同步**

每天使用sudo apt-mirror命令定时同步本地Ubuntu Source服务器和远程Ubuntu Source服务器获取最新的deb包。或者将这个命令写进crontab定时任务中。

```shell
$sudo vim /etc/cron.d/apt-mirror
0 4    * * *   /usr/bin/apt-mirror > /var/spool/apt-mirror/var/cron.log
```

每天四点执行同步，并在/var/spool/apt-mirror/var/文件夹中生成cron.log日志文件，以方便查看更新状态。



### **使用本地Ubuntu Source服务器**

- **修改sources.list文件**

现在，我们可以修改客户机/etc/apt/sources.list文件以使用本地Ubuntu Source服务器作为apt源。如果要将本机作为源服务器的话，将本机IP设置为静态IP，设置方法请查看[Ubuntu 16.04设置静态IP地址（NAT方式）](http://blog.csdn.net/lv18092081172/article/details/52081859)（如果存在不能上网问题，请仔细检查DNS服务器 /etc/resolv.conf）

本地IP为192.168.198.131，因此将客户机sources.list文件设置如下。

```shell
deb http://192.168.198.131/ubuntu/ xenial main restricted universe multiverse
deb http://192.168.198.131/ubuntu/ xenial-security main restricted universe multiverse
deb http://192.168.198.131/ubuntu/ xenial-updates main restricted universe multiverse
deb http://192.168.198.131/ubuntu/ xenial-backports main restricted universe multiverse
deb http://192.168.198.131/ubuntu/ xenial-proposed main restricted universe multiverse
deb-src http://192.168.198.131/ubuntu/ xenial main restricted universe multiverse
deb-src http://192.168.198.131/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://192.168.198.131/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://192.168.198.131/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://192.168.198.131/ubuntu/ xenial-proposed main restricted universe multiverse
```

- **重建索引**

```shell
sudo apt-get update
```

执行更新，以重建本地索引。以后就会使用本地的Ubuntu Source服务器了。

 

### **配置中遇到的问题**

我在最后一步重建本地索引的时候报错了。

找不到32位的包，可能是amd64中部分源使用了i386的。所以将 /etc/apt/mirror.list 中加入以下语句，然后重新apt-mirror下载32位deb包。再次执行apt-get update 的时候就没有错误啦。

```shell
deb-i386 http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb-i386 http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universemultiverse
deb-i386 http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universemultiverse
deb-i386 http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-i386 http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universemultiverse
```

