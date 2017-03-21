---
title: JAVA基础知识点摘录1
date: 2016-11-12 10:15:12
tags: [java]
---

摘录一点自己平时遇到的java知识点中模棱两可的地方，仅供参考。

### **JDK,JVM,JRE傻傻分不清楚**

**JDK (java development kit)**，就是java的开发工具集，顾名思义就是做开发用的，其中包括javac (java compiler)以及JRE (java 运行环境)等。jdk本身是由java编写的。

让我们看一下JDK的安装目录。在目录下面有 六个文件夹、一个src类库源码压缩包、和其他几个声明文件。其中，真正在运行java时起作用的 是以下四个文件夹：bin、include、lib、 jre。现在我们可以看出这样一个关系，JDK包含JRE，而JRE包含JVM。

- bin:最主要的是编译器(javac.exe)
- include:java和JVM交互用的头文件
- lib:类库
- jre:java运行环境

（注意：这里的bin、lib文件夹和jre里的bin、lib是不同的）总的来说JDK是用于java程序的开发，而jre则是只能运行class而没有编译的功能。eclipse、idea等 其他IDE有自己的编译器而不是用JDK bin目录中自带的，所以在安装时你会发现他们只要求你选中jre路径就ok了。

<!--more-->

**JVM ( Java virtual machine)**，就是java虚拟机，主要任务是把java字节码转成特定计算机的机器码，是实现java“一次编写，到处运行”的关键。程序源代码不是直接编译、链接成机器代码，而是先转化到字节码这种特殊的中间形式，字节码再转换成机器码或系统调用。前者是传统的编译方法，生成的机器代码就不可避免地跟特殊的操作系统和特殊的机器结构相关。 而 Java 程序的字节码文件可以放到任意装有 JRE 的计算机运行，再由不同 JRE 的将它们转化成相应的机器代码，这就实现了 Java 程序的可移植性。

**JRE (java runtime environment)**，就是java程序的运行环境。光有JVM还不能完成class的执行，因为在解释class的时候JVM需要调用解释所需要的类库lib。所写的java程序在经过javac编译后会形成字节码文件，这是java的中间文件，计算机是看不懂的，而这个文件是给JVM (java的虚拟机)用的，由JVM进行解释后形成机器语言给计算机，而这个JVM就在JRE中。JRE是运行Java程序必不可少的。

### **java 成员访问权限**


|           | 类内部  | package内部 |  子类  |  其他  |
| :-------: | :--: | :-------: | :--: | :--: |
|  public   |  √   |     √     |  √   |  √   |
| protected |  √   |     √     |  √   |  X   |
|  default  |  √   |     √     |  X   |  X   |
|  private  |  √   |     X     |  X   |  X   |

特别需要注意的，private修饰符对于**同一个类的任何实例**可见，对子类不可见，用于所在类本身。

```java
public class Complex{
  private double real;
  private double imaginary;
  public Complex(double r, double i){
    this.real = r;
    this.imaginary = i;
  }
  public boolean equals(Object o){
    if(this == o) return true;
    if(o == null || getClass() != o.getClass) return false;
    Complex complex = (Complex)o;
    if(Double.compare(complex.imaginary, imaginary) !=0) return false;
    if(Double.compare(complex.real, real) !=0) return false;
    return true;
  }
}
```

### **局部变量和成员变量**

在方法内定义的变量为局部变量，局部变量和累成员变量十分相似，但在使用上区别很大。

- 局部变量没有访问修饰符，不能用public、private、protected来修饰
- 局部变量不能用static修饰，没有”静态局部变量“
- 系统不会自动为局部变量赋初值，对于成员变量，系统会自动赋初值。基本类型初值为0，复合类初值为null
- 两者作用域不同，局部变量仅在方法内部有效
- 局部变量可以和成员变量同名，在使用时，局部变量具有更高的优先级

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

### **java对象传地址值调用**

java的对象参数采用传地址值调用，而非引用调用。举例如下。

```java
public class trySwap{
  public void swap(onlyTest a, onlyTest b)
    {
       onlyTest temp;
      temp = a;
      a = b;
      b = temp;
    }
}
public void showDiffer(){
  onlyTest ox = new onlyTest();
  onlyTest oy = new onlyTest();
  ox.setX(100);
  oy.setX(100);
  System.out.println("调用swap()方法之前的值：");
  System.out.println("ox.x=" + ox.getX()+",oy.x = "+oy.getX());
  swap(ox,oy);
  System.out.println("调用swap()方法之后的值：");
  System.out.println("ox.x=" + ox.getX()+",oy.x = "+oy.getX());
}
public static void main(String args[])
  {
    trySwap va = new trySwap();
  	va.showDiffer();
  }
```

如果是引用调用，那么调用swap方法将对实参ox和oy产生影响，实际运行后输出如下：

```java
调用swap()方法之前的值：
	ox.x = 100, oy.x = 200
调用swap()方法之后的值：
	ox.x = 100, oy.x = 200	
```

以上过程看出并非引用调用。调用过程如图。

![](http://ohjnxvaxm.bkt.clouddn.com/passing-value-swap.png)

### **本地方法**

在某些情况下，比如调用操作系统的API函数，需要调用比如C/C++等编写的代码，这些代码被称为本地(native)方法。下面实现用java调用C函数打印出消息"hello，native world"。

```java
public class HelloNative{
  public native static void greeting();
}
```

本地方法既可以是静态方法，也可以是实例方法。本地方法是允许带参数的。本地方法只需要方法的声明部分，不需要定义部分。定义部分由其他语言完成。

然后编写一个相应地C函数，必须按照JVM规定的方式给函数命名。命名规则不详细说了。可以使用JDK提供的javah程序，他能自动生成符合要求的函数名。使用javah之前，先用javac编译HelloNative.java文件，然后调用javah程序，生成C头文件。javah在jdk/bin目录下可找到。

```java
javah HelloNative
```

这会在当前目录下生成一个头文件HelloNative.h。然后将函数原型从标题文件中复制到C语言源文件中并包含标题文件，然后给出实现代码。

```c
#include "HelloNative.h"
#include <stdio.h>
JNIEXPORT void JNICALL Java_HelloNative_greeting(JNIEnv * env, jclass cl)
  {
    printf("hello, native world\n");
  }
```

因为java无法和静态文件链接到一起，只能采用动态链接库文件的形式。对C代码编译生成动态链接库。最后需要添加对System.loadLibrary方法的调用，先加载生成的动态链接库，然后调用greeting()方法。

```java
public class HelloNativeTest{
  public static void main(String args[])
    {
      System.loadLibrary("HelloNative");
      HelloNative.greeting();
    }
}
```

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

类初始化阶段,先执行**最顶层父类的静态初始化块**,依次向下,最后执行**当前类静态初始化块**,对象初始化阶段,先执行**最顶层父类的初始化块,构造器**,依次向下,最后执行**当前类初始化块,构造器**。

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

### **继承**

在创建一个导出类对象时，该对象包含了一个基类的子对象，该基类子对象被包装在导出类对象内部。

- **继承初始化顺序**

1. 初始化父类再初始化子类；
2. 先执行初始化对象中属性，再执行构造方法中的初始化。

- **super应用**

如果子类的构造方法中没有显式调用父类的构造方法，系统默认调用父类无参构造方法。如果没有默认的基类构造器，或者想调用一个带参数的基类构造器，必须用关键字super显式编写调用基类构造器的语句，并配以适当参数列表。调用基类构造器必须是在导出类构造器**第一行**。一个构造方法中只能有一条super语句。

- 属性的隐藏

子类变量可以修改继承下来的父类变量的**任何**属性，使用子类对象时，以修改之后的属性为准。

- 方法的覆盖

子类方法的访问权限可以与父类不同，但只允许权限更宽松，不允许更严格。遵循的是"公开的不再是秘密"这一原则。方法前面用final修饰，子类不能覆盖该方法。静态方法不允许被实例方法覆盖，实例方法不允许用静态方法覆盖。

协变返回类型是在子类中的被覆盖方法可以返回父类方法的返回类型的某种子类类型。

- 构造方法的继承

构造方法不能被static、final、synchronized、abstract、native修饰。子类可以自动调用父类的无参的构造方法，但是不会继承，也不会调用父类带参数的构造方法。

子类的构造方法在执行时，会**先调用父类的无参构造函数**。构造过程是从父类向外扩散的，所以父类在子类构造器可以访问它之前就完成了初始化。

### **多态**

java中所有方法都是通过后期动态绑定实现多态的，而域的访问时在编译期进行，不是多态的。

- 多态中构造器的调用顺序

1. 调用父类构造器
2. 按声明顺序调用成员的初始化方法
3. 调用子类的构造器主体

构造器的调用顺序是很重要的。

- **引用类型转换**

java向上转型时，会遗失子类特有的方法。向下转型需要使用**强制类型转换**。父类转型为子类时必须为指向子类对象的引用才进行转型，避免在执行向下转型时发生运行时ClassCastException异常，使用 **instanceof** 判断。

```java
A a = new B();
B b = (B)a;
```

 向上转型a的编译时类型为A，运行时类型为B。向下转型，编译时无错误，运行时无错误。

### **接口**

- 接口属性

接口修饰符建议为public，不能是protected和private。接口中属性都是**静态常量**，即使定义时不添加public static final修饰符，系统也会自动加上。

- 接口方法

接口中的方法都是public**抽象实例**方法，即使定义时不添加public abstract修饰符，系统也会自动加上。定义接口时如果不加abstract修饰符，系统也会自动加上。因为构造方法不能是抽象方法，所以接口没有构造方法。

- 多继承

Java中只能单继承，但是可以通过别的方式曲折实现多重继承 ，接口之间可以多继承，也可通过内部类方式实现多继承。接口在使用过程中，经常与匿名内部类一起使用。

java中只能继承一个类，但是可以实现多个接口，用","分割。如果继承父类，父类的继承要在接口的实现之前，即extends在implements前面。

- 成员属性同名

接口多继承时，可能会存在成员属性同名的情况，这是允许的，编译可通过。为了让编译器区分使用哪个变量，需在它前面加上一个接口名作为前缀。

- 成员方法同名

对于成员方法同名的情况，java会自动将其合并为一个方法，后面只需实现一个就够了。

### **内部类**

内部类对象会暗暗连接到创建它的外部类对象上，所以构建内部类对象需要一个指向外部类对象的引用。

```java
Out.In in = new Out().new In();
或
Out out = new Out(); 
Out.In in = out.new In();
```

如果创建的是静态内部类，则不需要对外部类对象的引用。

```java
Out.In in = new Out.In();
```

#### **匿名内部类**

只要一个类是抽象的或是一个接口，那么其子类中的方法都可以使用匿名内部类来实现。最常用的情况就是在多线程的实现上。

**匿名内部类基本实现**

```java
abstract class Person {
    public abstract void eat();
}
 
public class Demo {
    public static void main(String[] args) {
        Person p = new Person() {
            public void eat() {
                System.out.println("eat something");
            }
        };
        p.eat();
    }
}
```

**Runnable接口的匿名内部类实现**

```java
public class Demo {
    public static void main(String[] args) {
        Runnable r = new Runnable() {
            public void run() {
                for (int i = 1; i <= 5; i++) {
                    System.out.print(i + " ");
                }
            }
        };
        Thread t = new Thread(r);
        t.start();
    }
}
```

###**java I/O操作类**

![](http://ohjnxvaxm.bkt.clouddn.com/IO-STREAM.jpg)

I/O操作类大致可分为4组。

- 基于字节操作的I/O接口：InputStream和OutputStream;
- 基于字符操作的I/O接口：Writer和Reader;
- 基于磁盘操作的I/O接口：File;
- 基于网络操作的I/O接口：Socket;

InputStream和OutputStream及其子类以**字节**为单位对流数据进行处理；Reader和Writer及其子类以**字符**为单位对数据流进行处理，主要用于文本。这四个均为**抽象类**。

#### **文件处理类**

File，只能用于表示文件（目录）的信息（名称，大小等），不能用于访问文件内容。

#### **文件随机访问类**

RandomAccessFile，对文件内容的访问，可以任意访问文件的任何地方，write(int)和read(int)方法每次只能读写一个字节，从低位写起，同时指针指向下一个位置，准备再次读写。

#### **文件输入输出流**

FileInputStream/FileOutputStream，write(int)和read(int)方法每次只能读写低八位，可以通过批量读取效率更高write(byte[] b, int off, int len)。读取文件数据时，务必使用**int**类型的变量。

FileReader、FileWriter，字符文件处理流

#### **过滤输入输出流**

FilterInputStream和FilterOutputStream，抽象类，建立在**基本输入输出流**基础之上，对基本输入输出流传输的数据进行指定类型和格式的转换。这两个类的数据都是以**二进制**的形式处理。

子类有DataInputStream，DataOutputStream，是对"流"功能的扩展，更加方便地读取int,long,char等类型数据。

#### **缓冲流**

BufferedInputStream, BufferedOuputStream, BuffereaReader(提供了readLine方法，一次读一行，不能识别换行，需手动创建新行), BufferedWriter,PrintWriter(使用更方便，替换BufferedWriter，可以换行)，提供了带缓冲区的操作，提高了IO的性能。

#### **转换流**

InputStreamReader字节到字符的桥梁（按照编码方式）、OutputStreamWriter字符到字节的桥梁（按照编码方式）。

#### **控制台输入类**

Scanner

#### **对象序列化**

java平台允许我们在内存中创建可复用的Java对象，但一般情况下，只有当JVM处于运行时，这些对象才可能存在。但在现实应用中，可能要求在JVM停止运行之后能够保存(持久化)指定的对象，并在将来重新读取被保存的对象。Java对象序列化就能够帮助我们实现该功能。

对象的序列化主要有两种用途：
1） 把对象的字节序列永久地保存到硬盘上，通常存放在一个文件中。
2） 在网络上传送对象的字节序列。

**基本操作**

对象序列化就是将Object转换成byte序列，反之为对象的反序列化。必须注意地是，对象序列化保存的是对象的"状态"，即它的成员变量，对象序列化不包括static修饰的静态变量。

序列化流（ObjectOutputStream）是过滤流——writeObject，反序列化流（ObjectInputStream）——readObject。

在Java中，只有一个类实现了java.io.Serializable接口，那么它才可以被序列化。

```java
public class Person implements Serializable {
    private String name = null;
    private transient Integer age = null; 
    public Person() {
        System.out.println("none-arg constructor");
    }
    public Person(String name, Integer age) {
        System.out.println("arg constructor");
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public Integer getAge() {
        return age;
    }
    public void setAge(Integer age) {
        this.age = age;
    }

   @Override
    public String toString() {
        return "[" + name + ", " + age + "]";
    }
}
    SimpleSerial，是一个简单的序列化程序，它先将一个Person对象保存到文件person.out中，然后再从该文件中读出被存储的Person对象，并打印该对象。
public class SimpleSerial {
    public static void main(String[] args) throws Exception {
        File file = new File("person.out");
        ObjectOutputStream oout = new ObjectOutputStream(new FileOutputStream(file));
        Person person = new Person("John", 101);
        oout.writeObject(person);
        oout.close();

        ObjectInputStream oin = new ObjectInputStream(new FileInputStream(file));
        Object newPerson = oin.readObject(); // 没有强制转换到Person类型
        oin.close();
        System.out.println(newPerson);
    }
}
```

当Person对象被保存到person.out文件中之后，我们可以在其它地方去读取该文件以还原对象，但必须确保该读取程序的CLASSPATH中包含有Person.class(哪怕在读取Person对象时并没有显示地使用Person类，如上例所示)，否则会抛出ClassNotFoundException。

**transient**

如果类成员变量用transient关键字修饰的话，该元素不会进行jvm默认的序列化，但是可以自己完成这个元素的序列化，需要添加两个方法。

```java
private void writeObject(ObjectOutputStream s)throws IOException{
  s.defaultWriteObject();//把jvm能默认序列化的元素进行序列化
  s.writeInt(age);//自己完成序列化,因为此处为int类型，所以可用writeInt,也可以用writeObject
}
private void readObject(ObjectInputStream s)throws IOException,ClassNotFoundException{
  s.defaultReadObject();//把jvm能默认反序列化的元素进行反序列化操作
  this.age = s.readInt();//自己完成反序列化操作
}
```

**子父类构造函数问题**

如果父类实现了序列化接口，则子类不需要再实现序列化接口。

对Serializable对象反序列化时，并**不会调用任何构造函数** ，因此Serializable类无需默认构造函数，但是当Serializable类的父类没有实现Serializable接口时，反序列化过程会调用父类的默认无参构造函数，因此该父类必需有默认无参的构造函数，否则会抛异常。

### **java异常类**

所有的Java异常类都是由Throwable类派生出来的，层次结构如图。

![](http://ohjnxvaxm.bkt.clouddn.com/error-exception.jpeg)



**Error**是程序无法处理的错误，表示运行应用程序中较严重问题。大多数错误与代码编写者执行的操作无关，而表示代码运行时 JVM（Java 虚拟机）出现的问题。

**Exception**是程序本身可以处理的异常。Exception 类有一个重要的子类 RuntimeException。RuntimeException 类及其子类表示“JVM 常用操作”引发的错误。“如果出现RuntimeException ，则一定是你自己的问题”。

异常和错误的区别：异常能被程序本身可以处理，错误是无法处理。

#### **java的异常(包括Exception和Error)分类**

- 可查异常（编译器要求必须处置的异常）

正确的程序在运行中，很容易出现的、情理可容的异常状况。可查异常虽然是异常状况，但在一定程度上它的发生是可以预计的，而且一旦发生这种异常状况，就必须采取某种方式进行处理。

除了RuntimeException及其子类以外，其他的Exception类及其子类都属于可查异常。这种异常的特点是Java编译器会检查它，也就是说，当程序中可能出现这类异常，要么用try-catch语句捕获它，要么用throws子句声明抛出它，否则编译不会通过。

- 不可查异常(编译器不要求强制处置的异常)

包括运行时异常（RuntimeException与其子类）和错误（Error）。

#### **Exception分类**

- 运行时异常（非检测异常）

都是RuntimeException类及其子类异常，如NullPointerException(空指针异常)、IndexOutOfBoundsException(下标越界异常)等，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。这些异常大多由程序设计不当造成，程序应该从逻辑角度尽可能避免这类异常的发生。

运行时异常的特点是Java编译器不会检查它，也就是说，当程序中可能出现这类异常，即使没有用try-catch语句捕获它，也没有用throws子句声明抛出它，也会编译通过。

- 非运行时异常（检查异常）

除了RuntimeException以外的Exception。这是必须进行处理的异常，编译器一旦发现某些语句可能产生异常，就强制要求处理这些异常。如果不处理，程序就不能编译通过。如IOException、SQLException等以及用户自定义的Exception异常。

#### **throw和throws**

throw用来抛出异常，throws用来声明抛出异常。

一个方法中，如果使用throw抛出异常，要么自己捕获他，要么声明抛出了一个异常，交给上层处理。



