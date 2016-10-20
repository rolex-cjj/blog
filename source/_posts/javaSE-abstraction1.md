---
title: JAVA基础知识点摘录1
date: 2016-10-12 10:15:12
tags: [java]
---

摘录一点自己平时遇到的java知识点中不明了的地方，仅供参考。

### **JDK,JVM,JRE傻傻分不清楚**

**JDK (java development kit)**，就是java的开发工具集，顾名思义就是做开发用的，其中包括javac (java compiler)以及JRE (java 运行环境)等。jdk本身是由java编写的。

让我们看一下JDK的安装目录。在目录下面有 六个文件夹、一个src类库源码压缩包、和其他几个声明文件。其中，真正在运行java时起作用的 是以下四个文件夹：bin、include、lib、 jre。现在我们可以看出这样一个关系，JDK包含JRE，而JRE包含JVM。

- bin:最主要的是编译器(javac.exe)
- include:java和JVM交互用的头文件
- lib：类库
- jre:java运行环境

（注意：这里的bin、lib文件夹和jre里的bin、lib是 不同的）总的来说JDK是用于java程序的开发，而jre则是只能运行class而没有编译的功能。eclipse、idea等 其他IDE有自己的编译器而不是用JDK bin目录中自带的，所以在安装时你会发现他们只要求你 选中jre路径就ok了。

<!--more-->

**JVM ( Java virtual machine)**，就是java虚拟机，主要任务是把java字节码转成特定计算机的机器码，是实现java“一次编写，到处运行”的关键。程序源代码不是直接编译、链接成机器代码，而是先转化到字节码这种特殊的中间形式，字节码再转换成机器码或系统调用。前者是传统的编译方法，生成的机器代码就不可避免地跟特殊的操作系统和特殊的机器结构相关。 而 Java 程序的字节码文件可以放到任意装有 JRE 的计算机运行，再由不同 JRE 的将它们转化成相应的机器代码，这就实现了 Java 程序的可移植性。

**JRE (java runtime environment)**，就是java程序的运行环境。光有JVM还不能完成class的执行，因为在解释class的时候JVM需要调用解释所需要的类库lib。所写的java程序在经过javac编译后会形成字符码文件，这是java的中间文件，计算机是看不懂的，而这个文件是给JVM (java的虚拟机)用的，由JVM进行解释后形成机器语言给计算机，而这个JVM就在JRE中。JRE是运行Java程序必不可少的。

### **java 成员访问权限**




|           | 类内部  | package内部 |  子类  |  其他  |
| :-------: | :--: | :-------: | :--: | :--: |
|  public   |  √   |     √     |  √   |  √   |
| protected |  √   |     √     |  √   |  X   |
|  default  |  √   |     √     |  X   |  X   |
|  private  |  √   |     X     |  X   |  X   |



### **foreach中犯的错误**

今天写了一段自以为不会出错的代码。


```java
public class test4 {
   public static void main(String args[]){
      int [] a = new int[3];
      for(int j:a){
      j = 55;
      }
  }
}
```


结果发现数组中值都为0。通过查看java编译后的字节码发现，这几句代码是将数组引用副本中的值取出放在栈顶，然后从栈顶取出变量放在本地变量3中，然后把55放在栈顶，再把55取出放在本地变量3中，从始至终的操作都是在本地局部变量中的，并没有对原来的数值产生任何影响。

 故foreach语句是for语句特殊情况下的增强版本，简化了编程，提高了代码的可读性和安全性（不用怕数组越界）。foreach的优点在于可以遍历数组或者集合一遍，但是要引用数组或者集合的索引则无法做到。foreach一般结合泛型使用。

### **static**

- **static方法**

静态方法中可以直接调用同类中的静态成员，但不能直接调用非静态成员。若希望在静态方法中调用非静态变量，可以通过创建类的对象，然后 通过对象来访问非静态变量。

在普通成员方法中，则可以直接访问同类的非静态变量和静态变量。

静态方法中不能直接调用非静态方法，需要通过对象来访问访问非静态方法。

- **static初始化块**

在类的声明中，可以包含多个初始化块，当创建类的实例时，就会依次执行这些代码块。如果使用 static 修饰初始化块，就称为静态初始化块。

需要特别注意：**静态初始化块只在类加载时执行，且只会执行一次，同时静态初始化块只能给静态变量赋值，不能初始化普通的成员变量。**

```java
public class Root  
  {  
    static int num1;
    int num2;
    static{  
      num1 = 3;
      System.out.println("Root 的静态初始化块");  
    }  
    {  
      num2 = 4;
      System.out.println("Root 的普通初始化块");  
    }  
    public Root()  
    {  
      System.out.println("Root 的无参数的构造器");  
    }  
  }  

  public class Mid extends Root  
  {  
    static{  
      System.out.println("Mid 的静态初始化块");  
    }  
    public Mid()  
    {  
      System.out.println("Mid 的无参数的构造器");  
    }  
    public Mid(String msg)  
    {  
      //通过this调用同一类中重载的构造器  
      this();  
      System.out.println("Mid的带参构造器,其参数值:" + msg);  
    }  
  }  

  public class Test  
  {  
    public static void main(String[] args)  
    {  
      new Mid();  
      new Mid();  
    }  
  }  
```

类初始化阶段,先执行**最顶层父类的静态初始化块**,依次向下,最后执行**当前类静态初始化块**,对象初始化	阶段,先执行**最顶层父类的初始化块,构造器**,依次向下,最后执行**当前类初始化块,构造器**。

### **继承**

在创建一个导出类对象时，该对象包含了一个基类的子对象，该基类子对象被包装在导出类对象内部。

- **继承初始化顺序**

1. 初始化父类再初始化子类；
2. 先执行初始化对象中属性，再执行构造方法中的初始化。

- **super应用**

如果子类的构造方法中没有显式调用父类的构造方法，系统默认调用父类无参构造方法。如果没有默认的基类构造器，或者想调用一个带参数的基类构造器，必须用关键字super显式编写调用基类构造器的语句，并配以适当参数列表。调用基类构造器必须是在导出类构造器**第一行**。

### **final**

- **final属性**

该类的属性不会进行隐式初始化，类的初始化属性必须有值或者在构造方法中赋值，但只能选其一。

- **final变量**

一个即是static又是final的域只占一段不能改变的存储空间。只能赋一次值。

对基本类型使用final不能改变的是他的**数值**。而对于对象引用，不能改变的是他的**引用**，而**对象本身是可以修改的**。一旦一个final引用被初始化指向一个对象，这个引用将不能在指向其他对象。java并未提供对任何对象恒定不变的支持。这一限制也通用适用于**数组**，它也是对象。

- **final方法**

方法前面加上final关键字，代表这个方法不可以被子类的方法重写。

- **final类**

final类通常功能是完整的，它们不能被继承。

### **多态**

java中所有方法都是通过后期动态绑定实现多态的，而域的访问时在编译期进行，不是多态的。

- **引用类型转换**

java向上转型时，会遗失子类特有的方法。向下转型需要使用**强制类型转换**。父类转型为子类时必须为指向子类对象的引用才进行转型，避免在执行向下转型时发生运行时ClassCastException异常，使用 **instanceof** 判断。

```java
A a = new B();
B b = (B)a;
```

 向上转型a的编译时类型为A，运行时类型为B。向下转型，编译时无错误，运行时无错误。

### **接口**

接口修饰符建议为public，不能是protected和private。接口中属性是常量，即使定义时不添加public static final修饰符，系统也会自动加上。接口中的方法只能是抽象方法，即使定义时不添加public abstract修饰符，系统也会自动加上。定义接口时如果不加abstract修饰符，系统也会自动加上。

java中只能继承一个类，但是可以实现多个接口，用","分割。如果继承父类，父类的继承要在接口的实现之前，即extends在implements前面。

接口在使用过程中，经常与匿名内部类一起使用。

​