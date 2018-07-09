---
layout: post
title:  常用类
date: 2018-07-09 16:01:24.000000000 +09:00
---

>此节对Java常用类及其底层原理做一简单介绍

1.**String类**

- 字符串常量池：为了提升字符串的访问效率，Java使用了缓存技术，所有用字符串都会在字符串常量池中创建一份，字符串常量池在方法区被存储

- 执行过程中，如果程序用到某个字符串，会先在字符串常量池中去搜索该字符串，如果没有找到，则在常量池中创建一份，如果找到就直接拿来用

- String s1="abc"只会在字符串常量池中创建一个"abc"字符串对象

- String s2=new String("abc") 会在常量池中创建一个“abc字符串对象”，还会在堆内存中创建一个对象，第二种方式比较浪费内存，不推荐使用

- 尽量不要做字符串频繁的拼接操作，因为字符串一旦创建不可改变，只要频繁的拼接，就会在常量池中创建大量的字符串对象，给垃圾回收带来负担

- 若做字符串的拼接，建议采用StringBuffer，因为直接调用append方法，不会创建大量字符串对象

- StringBuffer是线程安全的，StringBuilder是非线程安全的

判断以下程序创建了几个对象

```
String s1=new String("chw");
String s2=new String("chw");

//3个对象   堆内存中两个   常量池中一个
```

-------

2.**基本数据类型包装类**

基本数据类型    包装类型
byte          java.lang.Byte
short         java.lang.Short
int           java.lang.Integer
long          java.lang.Long

float         java.lang.Float
double        java.lang.Double

boolean       java.lang.Boolean

char          java.lang.Character

![JAVA](https://raw.githubusercontent.com/cc20140820/cc20140820.github.io/master/assets/images/structure.png)

为何存在包装类？
```
//需求：规定m1方法可以接收java中任何一种数据类型
//m1方法如果想接收byte类型的数据，可以将byte类型先包装成java.lang.Byte再传递参数
public class Test {
    public static void main(String [] args){
        byte b= 10;
        Byte b1 = new Byte(b);
        m1(b1);
    }

    public static void m1(Object o){
        System.out.print(o);
    }
}

```

-------

3.**String,int,Integer三者相互转换**

- 深入自动装箱和自动拆箱：
    1.是程序编译阶段的概念，与程序运行无关
    2.主要目的是方便程序员的编码

```
//1.int-->Integer
Integer i1 = Integer.valueOf(10);

//2.Integer-->int
int i2=i1.intValue();

//3.String-->Integer
Integer i3=Integer.valueOf("10");

//4.Integer-->String
String s1=i3.toString();

//5.String-->int
int i4=Integer.parseInt(s1);

//6.int-->String
String s2=10+"";

//jdk5.0之后
Integer i5=10;//自动装箱
int i6=i5;//自动拆箱
```

-------

4.**日期类**

- Date类

```
Date d1=new Date();
SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String s1=sdf.format(d1);

//String-->Date
String strTime = "2018年08月08日 08:08:08 888";
SimpleDateFormat sdf1=new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss SSS");
try {
    Date d2 = sdf1.parse(strTime);
} catch (ParseException e) {
    e.printStackTrace();
}
```

- Calendar类

```
Calendar calendar = Calendar.getInstance();
int i = calendar.get(Calendar.DAY_OF_WEEK);
```
-------

5.**枚举**

```
public static Result divide(int a, int b){
    try {
        int c= a / b;
        return Result.SUCCESS;
    }catch (Exception e){
        return Result.FAIL;
    }
}

enum Result{
    //枚举要大写
    SUCCESS,FAIL
}
```

-------

6.**Random**

```
//生成5个不同的随机数【1-5】
public class RandomTest {
    public static void main(String [] args){
        int arr[]=new int[5];
        Random random = new Random();
        int index=0;
        while(index<5){
            int temp = random.nextInt(6);
            if(temp!=0 && !contains(arr,temp)){
                arr[index++]=temp;
            }
        }
    }

    private static boolean contains(int[] arr, int temp) {
        for(int i=0;i<arr.length;i++){
            if(arr[i]==temp)
                return true;
        }
        return false;
    }
}
```
