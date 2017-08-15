---
title: JAVA基础知识点摘录2
date: 2017-1-18 08:44:06
tags: [java]
---

### **String**

String类具有**不可变性**。使用 StringBuilder 或 StringBuffer 就可以避免这个问题。

StringBuilder 和StringBuffer，它们基本相似，不同之处，StringBuffer 是线程安全的，而 StringBuilder 则没有实现线程安全功能，所以性能略高。因此一般情况下，如果需要创建一个内容可变的字符串对象，应优先考虑使用 StringBuilder 类。

除了String类，所有数值类，Integer,Double,Character,BigInteger也是不可变的。

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
自动拆箱/装箱发生在基本类型值与对象需要相互转换的时候，也适用于表达式计算。在表达式中，对象会被自动拆箱参与计算，如果有必要，表达式的结果会被重新装箱。当包装器类型和基本数据类型进行“==”比较时，包装器类型会自动拆箱为基本数据类型。两个包装类对象比较时是比较地址。

在java中，会对-128到127的Integer对象进行缓存。当调用Integer.valueOf(int i)方法，如果-128<i<127，则直接返回常量池中的值，否则new一个。所以创建Integer对象使用该方法好一些。

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
### **java线程**

![](http://ohjnxvaxm.bkt.clouddn.com/thread-status.JPG)

#### **创建多线程**

创建线程有两种方法，继承Thread类和实现Runnable接口，使用接口方式可以避免单继承的局限。关键性操作有三个：

- 定义用户线程，即定义用户线程的run方法。
- 适当的时候建立用户线程实例，也就是用new来创建对象。
- 启动线程，调用线程对象的start方法。

继承Thread类方式

```java
public class ThreadDemo1 extends Thread {  
    private int countDown = 10;  
  
    @Override  
    // 在run方法中定义任务  
    public void run() {  
        ……
    }  
  
    public static void main(String[] args) {  
        ThreadDemo1 thread1 = new ThreadDemo1();
        ThreadDemo1 thread2 = new ThreadDemo1();
        thread1.start(); 
        thread2.start();   
        // 由于start方法迅速返回，所以main线程可以执行其他的操作,此时有两个独立的线程在并发运行  
        ……
    }  
}  
```

实现Runnable接口方式

```java
public class TreadDemo2 implements Runnable {  
    private int countDown = 10;  
    @Override  
    // 在run方法中定义任务  
    public void run() {  
        ……
    }  
  
    public static void main(String[] args) {  
        // Runnable中run方法是一个空方法，并不会产生任何线程行为，必须显式地将一个任务附着到线程上  
        Thread thread1 = new Thread(new TreadDemo2());  
        Thread thread2 = new Thread(new TreadDemo2());  
        thread1.start();  
      	thread2.start(); 
        ……
    }  
}  
```

采用继承Thread类方式：
（1）优点：编写简单，如果需要访问当前线程，无需使用Thread.currentThread()方法，直接使用this，即可获得当前线程。
（2）缺点：因为线程类已经继承了Thread类，所以不能再继承其他的父类。
采用实现Runnable接口方式：
（1）优点：线程类只是实现了Runable接口，还可以继承其他的类。在这种方式下，可以多个线程共享同一个目标对象，所以非常适合多个相同线程来处理同一份资源的情况，从而可以将CPU代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。
（2）缺点：编程稍微复杂，如果需要访问当前线程，必须使用Thread.currentThread()方法。

#### **同步机制常用方法**

java.util.concurrent.locks.Lock   lock(), unlock()

java.util.concurrent.locks.ReentrantLock  ReentrantLock()

java.util.concurrent.locks.Condition   await(), signalAll(), signal()

java.lang.Object    synchronized, wait(), notify(), notifyAll()

- **volatile**

如果声明一个域为volatile，那么编译器和虚拟机就知道该域是可能被另一个线程并发更新的。volatile变量可以提供可见性和有序性，不能提供原子性。

#### **线程安全类**

HashTable是线程安全的，HashMap不是线程安全的。

ConcurrentLinkedQueue是线程安全的，是基于链表实现的，所以他的size方法会遍历整个链表，所以不建议使用size方法。

Vector是线程安全的，ArrayList不是线程安全的。

StringBuffer是线程安全，StringBuilder不是线程安全的。

#### **中断和阻塞**

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


### **泛型**

#### **有界类型**

在创建泛型类时，需要对类型作出限制，可以为类型参数指定一个上界，声明所有的实际类型都必须是这个超类的直接或间接子类。

```java
class classname <T extends superclass>
```

也可以使用接口作为上界，关键字仍然是extends而非implements。一个类型可以有多个限界，限界类型用"&"分隔。在多个限界中可以有多个接口，但是最多只能有一个类，且该类为限界列表中第一个。

```java
class Stats<T extends Comparable & Serializable>
```

#### **通配符使用**

使用泛型类时，当调用某方法的对象和实际参数对象类型不一致时，可以使用通配符"?"。

```java
class Stats<T>
{
  ……
  void doSomething(Stats <T> ob)
  {
    System.out.println(ob.getClass().getName());
  }
}
Integer inums[] = {1,2,3,4,5};
Stats<Integer> iobj = new Stats<Integer>(inums);
Double dnums[] = {1.5, 2.5, 3.5, 4.5, 5.5};
Stats<Double> dobj = new Stats<Double>(dnums);
dobj.doSomething(iobj);//错误,形参和实参类型不同
//此时应该将doSomething方法声明变为void doSomething(Stats<?> ob)
```

通配符是用来声明一个泛型类的变量的，而不能创建一个泛型类。

#### **泛型接口**

如果一个类实现了一个泛型接口，则此类也是泛型类，否则无法接受传递给接口的类型参数。下面的声明是错误的。

```java
class Myclass implements MinMax<T>
```

在类Myclass中需要使用类型参数T，而类的使用者无法将它的实际参数传递进来，所以编译器报错。但是如果实现的是泛型接口的特定类型，则此类不再是泛型类。如下。

```java
class Myclass implements MinMax<Integer>
```

#### **泛型类RTTI**

泛型类也可以进行运行时类型识别。泛型类的对象总是一个特定的类型，，此时，它不再是泛型。所有的类型查询只会产生**原始类型**，无论是getClass()方法还是instanceof操作符。

泛型类也可以进行运行时类型识别。泛型类的对象总是一个特定的类型，，此时，它不再是泛型。所有的类型查询只会产生原始类型，无论是getClass()方法还是instanceof操作符。

例如，对象a是Generic\<Integer>类型，那么

```java
a instanceof Generic<?>
a instanceof Generic
```

两个测试结果都为真。尖括号中只能写通配符"?"，不能写确定的类型，实际上测试时，"?"会被忽略。

若b是Generic\<String>类型，getClass返回的也是原始类型。

```java
a.getClass() == b.getClass()//结果为真
```

#### **泛型类继承规则**

泛型类继承规则中，即使类型参数有继承关系，对应的泛型也没有任何关系。Generic\<Number>和Generic\<Integer>之间没有任何关系。

#### **类型擦除**

Java泛型的处理几乎都在编译器中进行，编译器生成的bytecode是不包含泛型信息的，泛型类型信息将在编译处理是被擦除，这个过程即类型擦除。

类型擦除指的是通过类型参数合并，将泛型类型实例关联到同一份字节码上。编译器只为泛型类型生成一份字节码，并将其实例关联到这份字节码上。类型擦除的关键在于从泛型类型中清除类型参数的相关信息，并且在必要的时候添加类型检查和类型转换的方法。
类型擦除可以简单的理解为将泛型java代码转换为普通java代码，只不过编译器更直接点，将泛型java代码直接转换成普通java字节码。
类型擦除的主要过程如下：

- 将所有的泛型参数用其最左边界（最顶级的父类型）类型替换。
- 移除所有的类型参数。

泛型类的静态变量是共享的，所以静态成员和静态方法都不能使用类型参数。

#### **泛型局限**

- 不能使用基本数据类型，必须是类类型；
- 不能使用泛型类异常，泛型类不能继承异常类；
- 不能使用泛型数组；
- 不能实例化参数类型对象，不能直接使用参数类型构造一个对象；

### **集合**

集合接口层次如下。

```java
java.util.Collection [I]
  +–java.util.List [I]
    +–java.util.ArrayList [C]
    +–java.util.LinkedList [C]
    +–java.util.Vector [C]
      +–java.util.Stack [C]
  +–java.util.Queue [I]
  	+–java.util.PriorityQueue[C]
  +–java.util.Set [I]
    +–java.util.HashSet [C]
      +–java.util.LinkedHashSet [C]
    +–java.util.SortedSet [I]
   	  +–java.util.TreeSet [C]
    +–java.util.EnumSet [C]

java.util.Map [I]
  +–java.util.SortedMap [I]
    +–java.util.TreeMap [C]
  +–java.util.Hashtable [C]
  +–java.util.HashMap [C]
    +–java.util.LinkedHashMap [C]
    +–java.util.WeakHashMap [C]

[I]：接口
[C]：类
```

#### **Iterator**

对于Iterator接口对象，调用next()方法之前必须先调用hasNext()方法进行测试，否则到了集合末尾，next()方法会抛出异常。

```java
Iterator iter = c.iterator();
while(iter.hasNext()){
	Object obj = iter.next();
}
```

迭代器的指针并不是直接指向某个元素，而是位于各个元素之间。使用remove()时，删除的是上次调用next()返回的元素，如果要删除某个位置上的元素，首先要跳过这个元素。调用remove()之前，必须保证调用了next()方法，不能连着两次remove()。

#### **常用类**

对于LinkedList，如果要遍历它，尽量使用for-each循环或者迭代器，不要使用get(int index)方法。

ArrayList长于随机访问元素，但在List中插入删除元素较慢；LinkedList通过代价较低的在List中插入删除元素，提供了优化的顺序访问，在随机访问方面相对较慢。

从HashSet中添加删除元素比从ArrayList中快。

TreeSet是一个有序树，排序算法比优先队列的插入排序法快很多。

在Collection中，sort()方法实现了归并排序，binarySearch()实现了二分查找。

Vector与ArrayList相似，但是它是线程安全的，在单线程下推荐使用ArrayList。

Hashtable与HashMap相似，但是它是线程安全的，在单线程下推荐使用HashMap。

#### **枚举类**

```java
enum Size{
  SMALL("S"),MEDIUM("M"),LARGE("L"),EXTRA_LARGE("XL");
  private String abbreviation;
  //定义私有构造器。无传入参数
  private Size()
    {
      this(null);
    }
  //定义私有构造器，传入字符串abbreviation作为参数
  private Size(String abbreviation)
    {
      this.abbreviation = abbreviation;
    }
  //定义公有的getAbbreviation方法，得到abbreviation的值
  public String getAbbreviation(){
    return abbreviation;
  }
}
```

这个Size类的构造方法都是私有的，所以不能在类外用Size创建对象。其中的

```java
SMALL("S")
```

相当于

```java
public static final Size SMALL = new Size("S");
```

### **Comparator和Comparable在排序中的应用**

#### **Comparable**

实现了该接口表示这个类的实例可以比较大小，可以进行自然排序，定义了**默认**的比较规则。实现此接口的对象列表（和数组）可以通过Collections.sort 或 Arrays.sort进行自动排序。是内部比较器。

接口方法：

```JAVA
/**
* @return 该对象小于、等于或大于指定对象o，分别返回负整数、零或正整数。 
*/ 
int compareTo(Object o);
```

```java
import java.util.Arrays;

public class ComparableTest {
    public static void main(String[] args) {
        Person[] persons = new Person[]{
            new Person(20, "P1"),
            new Person(60, "P2"),
            new Person(50, "P3"),
            new Person(40, "P4")
        };
        Arrays.sort(persons);
        System.out.println();
        //下面代码的结果一样
        //List<Person> personList = Arrays.asList(persons);
        //Collections.sort(personList);
        //System.out.println(personList);
    }
}

class Person implements Comparable<Person> {
    private int age;
    private String name;
    public Person(int age, String name) {
        this.age = age;
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    //实现Comparable接口的compareTo方法
    @Override
    public int compareTo(Person o) {
        return this.age - o.age;
    }
    
    @Override
    public String toString() {
        return "Person[name=" + name + ", age=" + age + "]";
    }
}
```

#### **Comparator**

用于定义**临时**比较规则，而不是默认比较规则。实现该接口的类都是一个比较器，一般在要比较的对象类型不支持自比较或者自比较函数不能满足要求时使用。可以将Comparator传递给Collections.sort或者Arrays.sort。使用此接口时，不需要在比较的类型中实现比较过程，而是把这个过程转移到了Comparator接口的compare方法中。是外部比较器。

接口方法：

```JAVA
/**
*@return o1小于、等于或大于o2，分别返回负整数、零或正整数。
*/
int compare(Object o1, Object o2)
boolean equals(Object obj);
```

```java
import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

public class ComparableTest {
    public static void main(String[] args) {
        Person[] persons = new Person[]{
            new Person(20, "P1"),
            new Person(60, "P2"),
            new Person(50, "P3"),
            new Person(40, "P4")
        };
        //Arrays.sort方法不支持使用Comparator比较器了，这里只能使用Collections.sort来排序
        List<Person> personList = Arrays.asList(persons);
        System.out.println("Before sort: \r\n" + personList);
        //这里，将一个比较器（Comparator）传递给sort方法作为参数，按照里面的比较逻辑对Person进行排序
        Collections.sort(personList, new PersonComparator());
        System.out.println("After sort: \r\n" + personList);
    }
}

//被比较的类型不需要实现任何接口
class Person { 
    private int age;
    private String name;
    public Person(int age, String name) {
        this.age = age;
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    @Override
    public String toString() {
        return "Person[name=" + name + ", age=" + age + "]";
    }
}

//这是一个比较器，用于比较Person对象
class PersonComparator implements Comparator<Person> {
    @Override
    public int compare(Person o1, Person o2) {
        //两个Person对象的比较过程，当然，这里可以实现更多更复杂的比较过程
        return o1.getAge() - o2.getAge(); 
    }
}
```

### **Class类的使用**

万事万物皆对象。任何类都是java.lang.Class类的实例对象。这个实例有三种表示方法。

```java
Class c1 = Foo.class;
Class c = Integer.TYPE;
```

第一种表示方式，告诉我们每个类都有一个隐含的静态成员class，T.class代表匹配的类对象。

如果是基本类型的封装类，还提供了一个TYPE作为类标记。

```java
Class c2 = foo1.getClass();
```

第二种表示方式，已经知道该类的对象可以通过getClass方法。

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
### **反射获取运行时类信息**

```java
Class c1 = int.class;
Class c2 = String.class;
Class c3 = double.class;
System.out.println(c1.getName());
System.out.println(c2.getName());
System.out.println(c3.getSimpleName());//不包含包名的类名称
```

#### **获取方法信息**

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
#### **获取成员变量信息**

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

#### **获取构造函数信息**

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
#### **方法反射的基本操作**

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
#### **执行构造函数创建新对象**

执行构造函数和普通方法有3个区别：

- 用getConstructor()获取构造方法替代getMethod()；
- 用newInstance()调用构造方法替代invoke()；
- 不需要获取返回值；

```java
import java.lang.reflect.*;
public class invokeConstructor{
  public invokeConstructor(){
    System.out.println("This is a constructor without parameter.");
    }
  public invokeConstructor(int a, int b)
    {
      System.out.println("a="+a+"b="+b);
    }
  public static void main(String args[])
    {
      try{
        Class cls = Class.forName("invokeConstructor");
        //创建参数类型列表
        Class partypes[] = new Class[2];
        partypes[0] = Integer.TYPE;
        partypes[1] = Integer.TYPE;
        //创建构造方法对象
        Constructor ct = cls.getConstructor(partypes);
        //创建实际参数数组
        Object arglist[] = new Object[2];
        arglist[0] = new Integer(37);
        arglist[1] = new Integer(47);
        //调用构造方法创建对象
        Object retoobj = ct.newInstance(arglist);
       }catch (Throwable e){
         System.out.println(e);
       }
    }
}
```

利用这种方法可以在程序运行时动态创建对象，而不是在编译的时候创建对象。

#### **改变属性值**

```java
import java.lang.reflect.*;
public class changeFields{
  public double d;
  public static void main(String args[])
    {
      try{
        Class cls = Class.forName("changeFields");
        //根据变量名获取Field对象
        Field fld = cls.getField("d");
        //用普通方法创建对象，以供验证
        changeFields f2obj = new changeFields();
        System.out.println("d = " + f2obj.d);
        //设置变量值
        fld.setDouble(f2obj, 12.34);
        System.out.println("d = " + f2obj.d);
      }catch(Throwable e)
        {
          System.out.println(e);
        }
    }
}
```

#### **使用数组**

```java
import java.lang.reflect.*;
public class useArray{
  public static void main(String args[])
    {
      try{
        //加载String类
        Class cls = Class.forName("java.lang.String");
        //创建String类型的数组对象，10个元素
        Object arr = Array.newInstance(cls, 10);
        //为0元素赋值
        Array.set(arr, 0, "this is a test");
        //获取0元素值
        String s = (String)Array.get(arr, 0);
        System.out.println("s = " + s);
      }catch(Throwable e)
        {
          System.out.println(e);
        }
    }
}
```

下面提供一个更复杂的例子

```java
import java.lang.reflect.;
public class useArray2{  
  public static void main(String args[])    
  {      
    try{        
      int dim[] = {5,10, 15};        
      //创建一个510*15的3维数组对象        
      Object arr = Array.newInstance(Integer.TYPE, dim);        
      //获取arr[3]        
      Object arrobj = Array.get(arr, 3);        
      //获取arr3        
      arrobj = Array.get(arrobj, 5);        
      //设置数组元素arrobj[10]为37        
      Array.setInt(arrobj, 10, 37);        
      int arrcast[] = (int [])arr;       
      System.out.println(arrcast3[10]);      
    }catch(Throwable e)        
    {          
      System.out.println(e);       
    }   
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
### **数据库连接**

**加载驱动**：Class.forName("com.mysql.jdbc.Driver")

**提供JDBC连接的URL**：String url = "jdbc:mysql://localhost:3306/test" 

**创建数据库连接**：Connection con = DriverManager.getConnection(String url , String username , String password)

**创建一个Statement**，执行SQL语句用： Statement stmt = con.createStatement()

**执行SQL语句**：

Statement接口提供了三种执行SQL语句的方法：executeQuery 、executeUpdate和execute   

```sql lite
ResultSet executeQuery(String sqlString)：执行查询数据库的SQL语句，返回一个结果集（ResultSet）对象。  
int executeUpdate(String sqlString)：用于执行INSERT、UPDATE或DELETE语句以及SQL DDL语句，如：CREATE TABLE和DROP TABLE等   
execute(sqlString):用于执行返回多个结果集、多个更新计数或二者组合的语句。   
```
```java
ResultSet rs = stmt.executeQuery("SELECT * FROM ...");   
int rows = stmt.executeUpdate("INSERT INTO ...");   
boolean flag = stmt.execute(String sql); 
```
**处理结果：**

```java
rs.next();
rs.getString("name");
```

**关闭连接：**

关闭记录集：rs.close

关闭声明：stmt.close()

关闭连接对象：conn.close()

### **注解**

注释是程序员对源代码的类，方法，属性等做的一些记忆或提示性描述(比如这个方法是做什么用的)，是给人来看的。
注解，语法以@开头，是Java编译器可以理解的部分，是给编译器看的。
**JDK自带注解**
@Override
@Deprecated(过时，弃用的)
@Suppvisewarnings(忽略某些警告)

- deprecation 使用了不赞成使用的类或方法时的警告
- unchecked 执行了未检查的转换时警告
- fallthrough 当使用switch操作时case后未加入break操作，而导致程序继续执行其他case语句时出现的警告
- path 当设置一个错误的类路径、源文件路径时出现的警告
- serial 当在可序列化的类上缺少serialVersionUID定义时的警告
- finally 任何finally子句不能正常完成时警告
- all 关于以上所有情况的警告

**注解分类**

- 源码注解，源码中存在，.class文件中不存在；
- 编译时注解，源码和class文件中都存在；
- 运行时注解，运行阶段依然起作用；
- JDK内置注解
- 第三方注解
- 自定义注解

**元注解**

元注解是注解的注解。
@Target 注解作用域，表示该注解用于什么地方，使用在类上，方法上，构造函数，属性等
@Retention 生命周期(源码，编译，运行)
@Inherited 允许子类继承父类中的注解，不会继承方法中注解，对于接口实现无效
@Documented 将此注解包含在javadoc中

使用@interface定义注解
public @inerface Description{
String desc();
String author();
int age() default 18;
}
成员以无参无异常方式定义声明，可用default为成员指定一个默认值，成员类型受限制，包括基本类型，String,Class,Annotation,Enummeration。只有一个成员时，成员名只能为value(),使用时可以忽略成员名和=。注解类可以没有成员，成为标识注解。

**注解使用**
@<注解名>(<成员名1>=<成员值1>,<成员名2>=<成员值2>)
@Description(desc="",author="",age=18)