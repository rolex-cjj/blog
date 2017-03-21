---
title: JSP基础知识摘录
date: 2016-10-26 11:14:55
tags: [jsp]
---

### **Servlet**

Servlet是JSP的前身。是在服务器端运行的小程序。一个Servlet就是一个java类，并可以通过“请求-响应”编程模型访问驻留在服务器内存里的Servlet程序。

#### **执行流程**

![](http://ohjnxvaxm.bkt.clouddn.com/servlet-execution.jpg)



<!--more-->

#### **生命周期**

1）初始化阶段，调用init方法，在servlet整个生命周期内，只调用一次。在执行构造方法之后。

2）响应客户请求阶段，调用service方法。由service方法根据提交方式选择执行doGet()或者doPost()方法。

3）终止阶段，调用destroy()方法。

![](http://ohjnxvaxm.bkt.clouddn.com/servlet-lifetime.jpg)

#### **servlet与JSP九大内置对象**

![](http://ohjnxvaxm.bkt.clouddn.com/servlet-nine-object.jpg)

### **JSP页面生命周期**


 ![](http://ohjnxvaxm.bkt.clouddn.com/jsp-lifetime.png)

### **JSP指令**

page：用于定义JSP文件中的全局属性，同一页面可有多个page指令<%@ page 属性="属性值"%>

include：在JSP文件被编译之前，将其他资源内嵌到当前文件中<%@ include file="filename"%>

taglib：定义一个标签库以及自定义标签的前缀

### **JSP脚本元素**

注释：\<!--comment  --> <%--  comment --%>

变量和方法声明：<%! declaration;[declaration;]%>

表达式：<%= expression %>

嵌入网页的程序段(Scriptlet)：<%  code fragment%>

### **JSP常用内置对象**

内置对象不需要创建实例，九大内置对象有out、request、response、session、application、page、pageContext、exception、config

out对象把结果输出到网页。

request对象获取用户数据。

response对象清除网页缓存。

session除非本次会话所有页面都关闭后再重新访问某个JSP或者Servlet将会创建新的会话。

application对象实现了用户间数据的共享，可存放全局变量。开始于服务器的启动，终止于服务器的关闭。

page对象就是指向当前JSP页面本身。

pageContext对象获取页面属性，能够存取其他隐含对象，比较强大。

exception对象是一个异常对象，当一个页面在运行过程中发生了异常，就产生这个对象。如果一个JSP页面要应用此对象，必须把isErrorPage设为true。

### **JSP动作元素**

重定向操作：\<jsp:forward page="URL"/>

​			等同于request.getRequestDispatcher("/url").forward(request,response);

包含操作：\<jsp:include page="URL"/>

param动作：\<jsp:param name="参数名" value="">常与forward动作一起使用，作为其的子标签。

嵌入插件：\<jsp:plugin>

创建Bean实例：\<jsp:useBean id="" class="" scope=""/>

设置Bean属性：\<jsp:setProperty name="javabean实例名" property="javabean属性名,*为所有属性" [value="beanvalue" param="request对象中的参数名"]/>(跟表单name相关)

获取Bean属性：\<jsp:getProperty name="javabean实例名" property="javabean属性名"/>

### **JavaBean**

一种特殊的java类。遵循了一定的设计原则。

1）公有类

2）有无参的公有构造方法

3）属性是私有的

4）用getter和setter方法封装属性

#### **scope四个作用域范围**

page 仅在当前页面有效

request 可通过HttpRequest.getAttribute()方法获取javabean对象

session 可通过HttpSession.getAttribute()方法获取javabean对象

application 范围最大，也可通过application.getAttribute()方法获取javabean对象

### **请求重定向和请求转发**

#### **请求重定向**

客户端行为，response.sendRedirect()，从本质上讲等同于两次请求。前一次的请求对象不会保存，地址栏的URL会改变。

#### **请求转发**

服务器行为，request.getRequestDispatcher().forward(req,resp)，是一次请求，转发后请求对象会保存，地址栏的URL地址不会改变。

### **session与cookie比较**

|       session        |       cookie        |
| :------------------: | :-----------------: |
|     在服务器端保存用户信息      |     在客户端保存用户信息      |
| session中保存的是Object类型 | cookie中保存的是String类型 |
|   随会话的结束而将其存储的数据销毁   |  cookie可以长期保存在客户端   |
|       保存重要的信息        |      保存不重要的信息       |

### **include指令与include动作比较**

|             |           include指令           |        include动作         |
| :---------: | :---------------------------: | :----------------------: |
|    语法格式     |    <%@ include file=".."%>    | \<jsp:include page=".."> |
|   发生作用时间    |      在JSP页面转换成Servlet之前       |         主页面被请求时          |
|    包含的内容    |          文件实际内容，即源代码          |         页面的输出结果          |
| 转换成的Servlet | 主页面和包含页面转换为一个Servlet（转换为同一个类） |   主页面和包含页面转换为独立Servlet   |
|    编译时间     |          较慢——资源必须被解析          |            较快            |
|    执行时间     |              稍快               |      较慢——每次资源必须被解析       |

**include指令：**它的主要优点是功能强大，所包含的代码可以含有总体上影响主页面的JSP构造，比如属性、方法的定义和文档类型的设定。它的缺点是难于维护只要被包含的页面发生更改，就得更改主页面，这是因为主页面不会自动地查看被包含的页面是否发生更改。

**include动作：**它的优点是在被包含的页面发生更改时，无须对主页面做出修改。它的缺点是所包含的是次级页面的输出，而非次级页面的实际代码，所以在被包含的页面中不能使用任何有可能在整体上影响主页面的JSP构造。

仅当include动作不能满足要求时，我们才应该使用include指令。对于文件包含，应该尽可能地使用**include动作**。仅在所包含的文件中定义了主页面要用到的字段或方法，或所包含的文件设置了主页面的响应报头时，才应该使用include指令。

