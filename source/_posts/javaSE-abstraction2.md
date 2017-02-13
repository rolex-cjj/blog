---
title: JAVA基础知识点摘录2
date: 2016-10-18 08:44:06
tags: [java]
---

### **String**

String类具有**不可变性**。使用 StringBuilder 或 StringBuffer 就可以避免这个问题。StringBuilder 和StringBuffer，它们基本相似，不同之处，StringBuffer 是线程安全的，而 StringBuilder 则没有实现线程安全功能，所以性能略高。因此一般情况下，如果需要创建一个内容可变的字符串对象，应优先考虑使用 StringBuilder 类。

### **包装类**

基本数据类型不具备对象特性，不能调用方法，所以java为每个基本数据类型都提供了一个包装类，使其具有对象特性。基本类型和包装类对应关系。

包装类主要提供了两大类方法：

1. 将本类型和其他基本类型进行转换的方法

2. 将字符串和本类型及包装类互相转换的方法

   <!--more-->

|  基本类型   |    包装类    |
| :-----: | :-------: |
|  byte   |   Byte    |
|  short  |   Short   |
|   int   |  Integer  |
|  long   |   Long    |
|  float  |   Float   |
| double  |  Double   |
|  char   | Character |
| boolean |  Boolean  |

装箱：把基本类型转换成包装类，使其具有对象的性质，又可分为手动装箱和自动装箱。

```java
int i = 10;
Integer x = new Integer(i);//手动装箱
Integer y = i;//自动装箱
```

拆箱：和装箱相反，把包装类对象转换成基本类型的值，又可分为手动拆箱和自动拆箱。

```java
Integer x = new Integer(8);
int i = x.intValue();//手动拆箱
int j = x;//自动拆箱
```
### **基本类型和字符串转换**

基本类型转换为字符串有三种方法：

1. 使用包装类的 toString() 方法  
2. 使用String类的 valueOf() 方法 
3. 用一个空字符串加上基本类型，得到的就是基本类型数据对应的字符串

```JAVA
int c = 10;
String str1 = Integer.toString(c);
String str2 = String.valueOf(c);
String str3 = c + "";
```

将字符串转换为基本类型有两种方法：

1. 调用包装类的 parseXxx 静态方法
2. 调用包装类的 valueOf() 方法转换为基本类型的包装类，会自动拆箱

```java
String str = "8";
int d = Integer.parseInt(str);
int e = Integer.valueOf(str);
```
### **Comparator和Comparable在排序中的应用**

**Comparator**

用于定义**临时**比较规则，而不是默认比较规则。强行对某个对象collection进行整体排序的比较函数，可以将Comparator传递给Collections.sort或者Arrays.sort。

接口方法：

```JAVA
/**
*@return o1小于、等于或大于o2，分别返回负整数、零或正整数。
*/
int compare(Object o1, Object o2);
```

**Comparable**

实现了该接口表示这个类的实例可以比较大小，可以进行自然排序，定义了**默认**的比较规则。

实现此接口的对象列表（和数组）可以通过Collections.sort 或 Arrays.sort进行自动排序。

接口方法：

```JAVA
/**
* @return 该对象小于、等于或大于指定对象o，分别返回负整数、零或正整数。 
*/ 
int compareTo(Object o);
```
### **java线程中断和阻塞**

参考[Java并发编程中的阻塞和中断](https://yq.aliyun.com/articles/38430)

- **中断：**指当出现需要时，CPU暂时停止当前程序的执行转而执行处理新情况的程序和执行过程。即在程序运行过程中，系统出现了一个必须由CPU立即处理的情况，此时，CPU暂时中止程序的执行转而处理这个新的情况的过程就叫做中断。

java中断机制是一种协作机制，也就是说通过中断并不能直接终止另一个线程，而需要被中断的线程自己处理中断。每个线程对象里都有一个boolean类型的标识，代表着是否有中断请求。

```JAVA
public static boolean interrupted 
//静态方法，测试当前线程中断状态是否被置位，同时清除线程的中断状态。
//换句话说，如果连续两次调用该方法，则第二次调用将返回false
//(在第一次调用已清除了其中断状态之后，且第二次调用检验完中断状态前，当前线程再次中断的情况除外)
  
public boolean isInterrupted() 
//实例方法，测试当前线程中断状态是否被置位。线程的中断状态不受该方法的影响。

public void interrupt() 
//中断线程。
```

如果线程被阻塞，将无法检测中断状态，这是产生InterruptedException异常的地方。

**安全停止线程方法是使用退出标志**。

```JAVA
public class ThreadFlag extends Thread 
{ 
  public volatile boolean exit = false; 

  public void run() 
  { 
    while (!exit); 
  } 
  public static void main(String[] args) throws Exception 
  { 
    ThreadFlag thread = new ThreadFlag(); 
    thread.start(); 
    sleep(5000); // 主线程延迟5秒 
    thread.exit = true;  // 终止线程thread 
    thread.join(); 
    System.out.println("线程退出!"); 
  } 
} 
```

- **interrupt方法**

  当一个线程对象调用interrupt()方法，它对应的线程并没有被中断，只是改变了它的**中断状态**。使当前线程的状态变为中断状态，如果没有其它影响，线程还会自己继续执行。此时自己调用isInterrupted()等检查中断状态会返回true。

  当线程因调用wait(),join(),sleep()等方法被阻塞时(存在不能被中断的阻塞I/O调用)，线程再调用interrupt方法会使中断状态被**清除**，当前线程会收到一个InterruptedException，使线程退出阻塞状态。**interrupt方法并不能正确停止线程**。

- **volatile**

如果声明一个域为volatile，那么编译器和虚拟机就知道该域是可能被另一个线程并发更新的。volatile变量不能提供原子性。

### **Class类的使用**

万事万物皆对象。任何类都是java.lang.Class类的实例对象。这个实例有三种表示方法。

```java
Class c1 = Foo.class;
```

第一种表示方式，告诉我们每个类都有一个隐含的静态成员class。

```java
Class c2 = foo1.getClass();
```

第二种表示方式，已经知道该类的对象通过getClass方法。

官网称c1,c2表示了Foo类的**类类型**（class type）。一个类只能是Class类的一个实例对象，即c1==c2为true。

```java
Class c3 = null;
try{
  c3 = Class.forName("com.immooc.reflect.Foo");
}catch(ClassNotFoundException e){
  e.printStackTrace();
}
```

第三种表示方式，通过Class.forName("xxx")引号中为类全称。我们可以通过类的类类型创建该类的对象实例，即通过c1创建Foo的实例。

```java
Foo foo = (Foo)c1.newInstance();//需要有无参数的构造方法
```
### **类的动态加载**

Class.forName("xxx")不仅表示了类的类类型，还表示了动态加载类，及运行时刻加载类。

```java
class OfficeBetter{
  public static void main(String[]args){
    try{
      //动态加载类，在运行时加载类
      Class c = Class.forName(args[0]);
      //通过类类型，创建该类对象
      OfficeAble oa = (OfficeAble)c.newInstance();
      oa.start();
    }
    catch(Exception e){
      e.printStackTrace();
    }
  }
}
interface OfficeAble{
  public void start();
}
class Word implements OfficeAble{
  public void start(){
    System.out.println("word..start");
  }
}
```

```java
javac Word.java
javac OfficeBetter.java //编译无错
java OfficeBetter Word  //运行时加载
```
### **java获取方法信息**

```java
Class c1 = int.class;
Class c2 = String.class;
Class c3 = double.class;
System.out.println(c1.getName());
System.out.println(c2.getName());
System.out.println(c3.getSimpleName());//不包含包名的类名称
```

**获取方法信息：**

```java
public class ClassUtil{
  public static void printClassMessage(Object obj){
    Class c = obj.getClass(); //传递哪个子类的对象，c就是该子类的类类型
    System.out.println(c.getName());
    /*
    *java.lang.reflect.Method类，方法对象，一个成员方法就是一个Method对象
    *getMethods()方法获取的是所有public函数，包括父类继承而来的
    *getDeclaredmethods()获得所有该类自己声明的方法，不问访问权限
    */
    Method[] ms = c.getMethods();//c.getDeclaredMethods();
    for(int i = 0; i < ms.length;i++){
      //得到方法返回值类型的类类型（xx.class）
      Class returnType = ms[i].getReturnType();
      System.out.print(returnType.getName()+" ");
      //得到方法全名
      System.out.print(ms[i].getName()+"(");
      //获取参数类型，得到的是参数列表的类型的类类型
      Class[] paramTypes = ms[i].getParameterTypes();
      for(Class class1:paramTypes){
        System.out.print(class1.getName()+",");
      }
      System.out.println(")");
    }
  }
}
```
### **java获取成员变量构造函数信息**

**获取成员变量信息：**

```java
/*
*成员变量也是对象，java.lang.reflect.Field.
*Field类封装了关于成员变量的操作
*getFields()方法获取的是所有public成员变量的信息
*getDeclaredFields()获取自己声明的成员变量信息
*/
  public static void printFieldMessage(Object obj){
    Class c = obj.getClass(); //传递哪个子类的对象，c就是该子类的类类型
    Field[]fs = c.getDeclaredFields();
    for(Field field : fs){
      //得到成员变量的类型的类类型
      Class fieldType = field.getType();
      String typeName = fieldType.getName();
      //得到成员变量的名称
      String fieldName = field.getName();
       System.out.println(typeName+" "+fieldName);
    }
  }
```

**获取构造函数信息：**

```java
/*
*构造函数也是对象，java.lang.reflect.Constructor.
*Constructor类封装了关于成员变量的操作
*getConstructors()方法获取的是所有public构造函数的信息
*getDeclaredConstructors()获取自己声明的构造函数信息
*/
  public static void printConMessage(Object obj){
    Class c = obj.getClass(); //传递哪个子类的对象，c就是该子类的类类型
    Constructor[]cs = c.getDeclaredConstructors();
    for(Constructor con : cs){
      //得到构造函数的名称
      System.out.println(con.getName()+"(");
      Class[] paramTypes = con.getParameterTypes();
      for(Class class1:paramTypes){
        System.out.print(class1.getName()+",");
      }
      System.out.println(")");
    }
  }
```
### **方法反射的基本操作**

```java
public class MethodDemo1 {

  public static void main(String[] args) throws IllegalAccessException, IllegalArgumentException, InvocationTargetException {
    // TODO Auto-generated method stub
    A a1 = new A();
    Class c = a1.getClass();
    try {
      //获取方法  名称和参数列表来决定
      //c.getMethod("print",new Class[]{int.class,int.class});
      Method m = c.getMethod("print",int.class,int.class);

      //a1.print(10,20);
      //方法的反射操作,用m对象来进行方法调用
      //方法如果没有返回值，返回null，有则返回具体返回值
      //Object o = m.invoke(a1,new Object[]{10,20});
      Object o = m.invoke(a1,10,20);

    } catch (NoSuchMethodException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    } catch (SecurityException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    }

  }
}
class A{
  public void print(int a, int b){
    System.out.println(a+b);
  }
  public void print(String a, String b)
  {
    System.out.println(a.toUpperCase()+","+b.toUpperCase());
  }
}
```
### **通过反射了解集合泛型本质**

反射的操作都是**编译之后**的操作。

```java
ArrayList list = new ArrayList();
ArrayList<String> list1 = new ArrayList<String>();
//list1.add(20);编译错误
list1.add("hello");
Class c1 = list.getClass();
Class c2 = list1.getClass();
System.out.println(c1 == c2);	
```

c1 == c2结果返回true说明**编译之后集合的泛型是去泛型化**的。
java中集合的泛型，是防止错误输入的，只在编译阶段有效，绕过编译就无效了。

验证：可以通过方法的反射来操作，绕过编译。

```java
Method m = c2.getMethod("add", Object.class);
m.invoke(list1,20);//添加成功
System.out.println(list1.size());
System.out.println(list1);
/*
* for(String string:list1){
* 	System.out.println(string);
* }//现在不能这么遍历
*/
```