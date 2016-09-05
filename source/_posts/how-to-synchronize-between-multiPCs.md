---
title: Github实现不同电脑下hexo博客的同步
date: 2016-09-02 13:56:42
tags: 
- github
- hexo
-  blog
---



hexo+github如何建立自己的博客在上节中已经讲过了，详情请见[如何用Hexo搭建Github静态博客](https://rolex-cjj.github.io/2016/08/30/how-to-build-my-own-blog-by-hexo/)

本节将介绍如何在不同电脑上同步本地的博客。假设A电脑为建立博客的设备，B电脑为新设备。

## A电脑中从本地上传Hexo到Github仓库

- **初始化仓库**

在Hexo博客的根目录运行Git Bash并输入以下命令：

```
$ git init
$ git remote add origin <server address>
```

 这里<server>指的是在线仓库的地址，比如在这里我的就应该是https://github.com/rolex-cjj/blog.git, 如果你用其它git仓库服务，填写对应仓库地址即可.

origin是本地分支,remote add会将本地仓库映射到Github仓库

<!--more-->

- **把本地文件同步到Github上面** 

分别输入执行以下命令：

```
$ git add .  #添加所有目录，注意add后面有个点`.`
$ git commit -m "add to Github" #添加提交说明，每次提交都需要
$ git push -u origin master   #把更新推送到云端
```

这时可以登录Github账户查看刚创建的blog仓库中是否上传成功
windows平台可能push过程中会提示输入Github的用户名和Github的密码，输入正确便是。

**Note:**

为了在另一台电脑上配置更加方便，严重建议把Hexo博客目录下_config.yml文件复制粘贴一份，并重命名为hexo_config.yml；把themes目录下你用到主题目录下的_config.yml文件也复制一份，并粘贴到**博客根目录**，并命名为theme_config.yml。原因是我们上传的时候，我们自己安装的themes如：[yilia](https://github.com/rolex-cjj/hexo-theme-yilia.git)，它的'yilia'目录并不能上传，所以我们需要把这两个配置文件都保存下来在进行同步工作。

## B电脑从Github仓库取回Hexo到本地

- **把文件取回本地**

安装环境完成后，在新文件夹下运行Git Bash并分别执行以下几条命令：

```
$ git init
$ git remote add origin <server address>
$ git fetch –all
$ git merge origin/master
```

这里<server>仍然是你的Giuhub地址。fetch是将仓库中的内容取出来。reset则是不做任何合并（merge）处理，直接把取出的内容保存。运行完merge命令后你会发现文件夹中就会出现刚刚上传的内容。

- **安装主题，同时更新config文件**

此时在B电脑上就配置成功了。

## B电脑上的Hexo从本地同步到Github仓库

```
$ git add .
$ git commit -m "commit from PC_B"
$ git push -u origin master
```



## A电脑更新本地博客

  `$ git pull https://github.com/xxxx/xxx.git`

**Note:**

我们每次更新博客时，为了保持我们每次用到的程序都是最新的。

每次更新博客之前都需要执行

`$ git pull https://github.com/xxxx/xxx.git`

保持本地最新；

每次更新博客之后都需要执行

```
$ git add .
$ git commit -m "message"
$ git push -u origin master
```

以保持Github仓库程序最新。

现在我们就能实现在不同电脑都能对我们的Hexo博客进行维护了。

## 参考资料

[在Github上面搭建Hexo博客: 使用不同电脑维护](http://mungo.space/2015/10/14/create-hexo-on-github-4/)

## 友情链接

[Qi's Blog ](https://nextinnovationucas.github.io/)