---
title: 位运算的特殊用法
date: 2016-10-31 09:25:15
tags: [位运算]
---

在计算机中，使用位运算代替一些逻辑运算，可以大大加快运算的速度。所以掌握位运算的一些特殊用法大有必要。

### **简单位运算**

#### **按位与&**

**清零**：如果想将一个单元清零，只要与一个各位都为零的数值想与，结果为零。

**取一个数中指定位**：找一个数，对应X要取得位，该数的对应位为1，其余位为零，次数与X进行“与运算”可以得到X中的指定位。

#### **按位或|**

**将一个数据的某些位置1**：找一个数，对应X要置1的位，该数的对应位为1，其余位为零，该数与X相或可使X中的某些位置1。

<!--more-->

#### **异或^**

**使特定位翻转**：找一个数，对应X要翻转的各位，该数的对应位为1，其余位为零，此数与X对应位异或即可。

**与0异或，保留原值**：可以用来交换两个变量值。

**两个变量交换值的方法**

- 借助第三个变量：C=A;A=B;B=C;
- 利用加减法：A=A+B;B=A-B;A=A-B;
- 用按位异或运算，效率最高：A=A^B;B=A^B;A=A^B;

#### **左移<<**

若左移时舍弃的高位不包含1，则每左移一位，相当于该数乘以2。

#### **右移>>**

正数左补0，负数左补1。每右移一位，相当于该数除以2。

### **位运算实现加减乘除**

#### **加法运算**

```java
int add(int a, int b)  
{
  int result;  
    while(b)       // 直到没有进位  
    {  
        result = a^b;       // 不带进位的加法  
        b = ((a&b)<<1);  // 进位  
        a = result;  
    }  
    return a;  
    /*简化版
    return b==0 ? a: add(a^b,(a&b)<<1);  
    */
} 
```

思路就是先不计进位相加，然后再与进位相加，随着递归，进位会变为0，递归结束。

#### **负数表示**

```java
int negtive(int a)   //取补码  
{  
    return add(~a, 1);  
} 
```

二进制中，一个负数可以表示为正数的补码形式，即正数的反码加1。

#### **减法运算**

```java
int sub(int a,int b)  
{  
    return add(a,negative(b));  
}
```

减法和加法一样，加数为减数的相反数。

#### **判断为0**

```java
int isZero(int a)
 {
   return !(a&0xffff);
 }
```

#### **判断正数**

```java
int isPositive(int a)
{
  return (a&0xffff)&& !(a&0x8000);
}
```

#### **判断负数**

```java
int isNegative(int a)  
{  
    return a&0x8000;   
}  
```

#### **正数乘法**

```java
int pos_multi(int a, int b)
{
  int result = 0;    
    while(b)    
    {    
        if(b&1) 
          result = add(result, a);    
        a <<= 1;    
        b >>= 1;    
    }    
    return result;   
}
```

#### **乘法**

```java
int multi(int a, int b)
{
      if(isNegative(a))  
    {  
        if(isNegative(b))  
            return pos_multi(negative(a),negative(b));  
        else  
            return negative(pos_multi(negative(a),b));  
    }  
    else  
    {  
        if(isNegative(b))  
            return negative(pos_multi(a,negative(b)));  
        else  
            return pos_multi(a,b);  
    }
}
```

#### **正数除法**

```java
int pos_div(int a,int b)    
{    
    if(a<b) return 0;  
    if(a==b) return 1;  
    int result=0;   
    //第32位为符号位，所以从第31位开始  
    for(int i=31; i>=0; i--)    
    {    
        if((a>>i)>=b)//比较a是否大于等于b的（1<<i）次方，避免将a与（b<<i）比较，因为b<<i可能会溢出。
        {    
            result = add(result,1<<i);    
            a = sub(a,b<<i);    
        }    
    }    
    return result;    
} 
```

除法就是乘法的逆过程，依次减掉（如果a够减的话）(2^31)b,(2^30)b,...,8b,4b,2b,1b。减掉相应数量的b就在结果加上相应的数量。 

#### **除法**

```java
int div(int a, int b)
{
  if(isNegative(a))  
    {  
        if(isNegative(b))  
            return pos_div(negative(a),negative(b));  
        else  
            return negative(pos_div(negative(a),b));  
    }  
    else  
    {  
        if(isNegative(b))  
            return negative(pos_div(a,negative(b)));  
        else  
            return pos_div(a,b);  
    }  
}
```

#### **比较两个正数的大小**

```java
int isbig_pos( int a, int b )   //a是否是最大的数
{  //a,b>0  
    int c = 1;  
    b = (a^b);  
    if( isZero(b) )  
        return 0;  //相等
    while( b >>= 1 )  
    {  
        c <<= 1;  
    }  
    return (c&a);  
}
```

#### **比较两个数的大小**

```java
int isbig( int a, int b )   
{   
    if( isNegative(a) )  
    {  
        if( isNegative(b) )  
        {  
            return isbig_pos( negtive(b), negtive(a) );  
        }  
        return 0;  
    }  
    if( isNegative(b) )  
        return 1;  
    return isbig_pos(a, b);  
} 
```