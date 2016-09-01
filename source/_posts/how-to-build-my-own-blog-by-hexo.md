---
title: 如何用Hexo搭建Github静态博客
date: 2016-08-30  12:55:59
tags: 
- github
- hexo
-  blog
---

# **环境搭建** #

## 配置github ##

-  **注册github账号**

-  **配置SSH-KEY**

打开git bash，执行以下命令

    $ ssh-keygen -t rsa -C "youremail@163.com"

输入安装目录及口令，找到目录下.ssh文件夹下id_rsa.pub文件

登陆GitHub->Settings->“SSH Keys”，然后，点“Add SSH Key”，输入Title，在Key文本框里粘贴id_rsa.pub文件的内容，点“Add Key”。

也可以参考[Generating an SSH key](https://help.github.com/articles/generating-an-ssh-key/)

-  **建立仓库**

建立与你用户名对应的仓库，仓库名为【your_user_name.github.io】
<!--more-->

## 安装 [git(for windows)](https://git-scm.com/download/win)##

## 安装[node.js](https://nodejs.org/en/) ##

## 安装Hexo ##

- **安装**

打开git bash，执行如下命令

    $ npm install -g hexo


- **部署**

   **Setup your blog**

在电脑中建立一个名字叫「Hexo」的文件夹（比如我建在了E:\workspace\hexo），然后在此文件夹中右键打开Git Bash。执行下面的命令。
    
        $ hexo init
        INFO  Cloning hexo-starter to E:\workspace\hexo
      	Cloning into 'E:\workspace\hexo'...
     	……
    	INFO  Start blogging with Hexo!

Hexo随后会自动在目标文件夹建立网站所需要的文件。

再输入以下命令，安装依赖文件

    $ npm install

会在E:\workspace\hexo目录中安装 node_modules。


**Start the server**

运行下面的命令

    $ hexo server
    INFO  Start processing
    INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.

表明Hexo Server已经启动了，在浏览器中打开http://localhost:4000, 这时可以看到Hexo已为你生成了一篇blog。你可以按Ctrl+C 停止Server。

 **Create a new post**

停止server，在 E:\workspace\hexo 新打开一个git bash命令行窗口，执行以下命令

    $ hexo new "My New Post"
    INFO  Created: E:\workspace\hexo\source\_posts\My-New-Post.md

Hexo Server启动，在浏览器中打开http://localhost:4000, 可以发现已生成了一篇新文章 "My New Post"。


 **Generate static files**

执行下面的命令，将markdown文件生成静态网页。

    $ hexo generate
该命令执行完后，会在E:\workspace\hexo\public\ 目录下生成一系列html，css等文件。

 **编辑文章**

hexo new "My New Post"会在E:\workspace\hexo\source\_posts目录下生成一个markdown文件：My-New-Post.md

可以使用一个支持markdown语法的编辑器（比如 MarkDownPad2）来编辑该文件。
 Note:
win10下安装MarkDownPad2时需要安装库文件 Awesomium 1.6.6 SDK，详情见[MarkDownPad](http://markdownpad.com/)

 **部署到github**

部署到Github前需要配置该目录下_config.yml文件，首先找到下面的内容

    # Deployment
    ## Docs: https://hexo.io/docs/deployment.html
    deploy:
      type:

将他们改为

    # Deployment
    ## Docs: http://hexo.io/docs/deployment.html
    deploy:
      type: github
      repository: https://github.com/rolex-cjj/rolex-cjj.github.io.git
      branch: master

**Note:**

如果你是为一个项目制作网站，那么需要把branch设置为gh-pages。


 **将其 deploy 到仓库中**

依次执行以下命令

    $ hexo clean
    $ hexo generate
    $ hexo deploy

**Note:**

如果出现下图错误，

    $ hexo deploy
    ERROR Deployer not found: github

deploy 的 type 改成 git，然后在 Git Bash 中运行以下命令

    $ npm install hexo-deployer-git --save

再重新执行以下命令

    $ hexo clean
    $ hexo generate
    $ hexo deploy

第一次使用deploy可能出现以下提示

    Run
    
      git config --global user.email "you@example.com"
      git config --global user.name "Your Name"
    
    to set your account's default identity.
    Omit --global to set the identity only in this repository.

执行命令输入自己的github用户名和邮箱验证      

    git config --global user.email "you@example.com"
    git config --global user.name "Your Name"

重新执行上述三条命令进行部署

 **测试**

当部署完成后，在浏览器中打开http://rolex-cjj.github.io/（https://rolec-cjj.github.io/）, 正常显示网页，表明部署成功。

- **总结部署步骤**

每次部署的步骤，可按以下三步来进行。

    $ hexo clean
    $ hexo generate
    $ hexo deploy

-  **本地调试**

在执行下面的命令后

    $ hexo g #生成
    $ hexo s #启动本地服务，进行文章预览调试

浏览器输入http://localhost:4000, 查看搭建效果。此后的每次变更_config.yml 文件或者新建文件都可以先用此命令调试，尤其是当你想调试新添加的主题时。
  
以上命令可以简化为一条命令

    $ hexo s -g

- **主题安装**

Hexo提供了很多主题，参见 [Themes](https://hexo.io/themes/)，本步骤以minos主题为例


 **安装步骤**

点击主题名获取主题的web url，将Git Shell 切到E:\workspace\hexo\目录下，然后执行下面的命令，将minos下载到 themes/minos 目录下

    $ git clone https://github.com/ppoffice/hexo-theme-minos.git themes/minos

修改你的博客根目录下的config.yml配置文件中的theme属性，将其设置为minos

    # Extensions
    ## Plugins: https://hexo.io/plugins/
    ## Themes: https://hexo.io/themes/
    theme: minos

 **更新主题**

    cd themes/minos
    git pull

回到根目录，执行以下命令本地预览

    $ hexo s -g

再执行三条命令部署到github上

    $ hexo clean
    $ hexo generate
    $ hexo deploy

**Note:**

根目录下的配置文件config.yml中还可以设置很多属性，这里就不再赘述了，可以参考[hexo 配置](https://hexo.io/zh-cn/docs/configuration.html)  以及 [hexo使用攻略](http://ijiaober.github.io/2014/08/05/hexo/hexo-04/)，请各位自己去探索吧！


## 参考资料 ##

[Hexo搭建Github静态博客](http://www.cnblogs.com/zhcncn/p/4097881.html)

[Windows下一步步搭建自己的独立博客——使用 GitHub Pages + Hexo 基础教程（一）](http://www.jianshu.com/p/985d07d88ef4)

## 友情链接 ##

[在Github上面搭建Hexo博客: 使用不同电脑维护](http://mungo.space/2015/10/14/create-hexo-on-github-4/)
