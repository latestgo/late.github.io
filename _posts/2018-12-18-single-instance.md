---
layout: post
title: "单例设计模式"
categories: Java
tag: Singleton
author: "latestgo"
---

所谓的单例设计模式指的是一个类只允许产生一个实例化对象。

## 单例设计模式

**范例：** 一个简单程序
```java
class Singleton {
    public void print() {
        System.out.println("Hello world !");
    }
}
public class TestSingleton {
    public static void main(String[] args) {
        Singleton s = null; //声明对象
        s = new Singleton(); //实例化对象
        s.print();
    }
}
```
以上程序在实例化的时候调用了Singleton类的无参构造方法，因为在在Singleton类里面并没有提供任何的构造方法，所以会自动一个无参的，什么都不做的构造方法。但是如果现在将Singleton类明确定义一个私有的构造方法。
```java
class Singleton {
    private Singleton() {} //所有的方法都可以使用四种访问权限
    public void print() {
        System.out.println("Hello world !");
    }
}
```
这个时候类中明确的提供有一个私有的构造方法，那么默认无参的构造方法将不会自动生成，那么此时再进行对象实例化的时候一定会有错误。
>TestSingleton.java:10: 错误: Singleton()可以在Singleton中访问private
>        s = new Singleton(); //实例化对象

首先一旦构造方法被私有化了，那么就表示外部无法调用构造了，也就证明外部不能产生新的实例化对象。此时的类是一个相对而言封闭的状态了。

可是如果要想继续调用Singleton类中print()普通方法，那么就必须提供有实例化对象。于是根据封装的特点，可以考虑在类的内部产生一个实例化对象。
```java
class Singleton {
    //在类的内部是可以访问私有的结构
    Singleton instance = new Singleton();//内部产生实例化对象
    private Singleton() {} //所有的方法都可以使用四种访问权限
    public void print() {
        System.out.println("Hello world !");
    }
}
```
现在Singleton类内部的instance对象（属性）是一个普通属性，所有的普通属性必须在有实例化对象的时候才有内存空间的分配，而现在外部无法产生实例化对象，所以在Singleton类在没有实例化对象产生的时候也可以将instance进行使用。这时候就需要`static`关键字。
```java
class Singleton {
    //在类的内部是可以访问私有的结构
    static Singleton instance = new Singleton();//内部产生实例化对象
    private Singleton() {} //所有的方法都可以使用四种访问权限
    public void print() {
        System.out.println("Hello world !");
    }
}
public class TestSingleton {
    public static void main(String[] args) {
        Singleton s = null; //声明对象
        s = Singleton.instance; //实例化对象
        s.print();
    }
}
```
以上虽然可以取得了Singleton类的实例化对象，但是对于类中的属性应使用private封装，而如果要想取得封装的属性，就必须提供一个getter方法，而访问的是一个static属性，并且这个属性无法在外部进行实例化对象的创建，所以应该在提供一个static方法，因为static方法也不受实例化的控制。
```java
class Singleton {
    //在类的内部是可以访问私有的结构
    private static Singleton instance = new Singleton();//内部产生实例化对象
    public static Singleton getInstance() {
        return instance;
    }
    private Singleton() {} //所有的方法都可以使用四种访问权限
    public void print() {
        System.out.println("Hello world !");
    }
}
public class TestSingleton {
    public static void main(String[] args) {
        Singleton s = null; //声明对象
        s = Singleton.getInstance(); //实例化对象
        s.print();
    }
}
```
这样的目的只希望类中产生唯一的实例化对象。

对于单例设计模式，也有两类形式：懒汉式、饿汉式；以上程序是饿汉式，不管是否使用Singleton类对象，只要该类加载了，那么一定会自动创建好一个公共的instance对象。

**范例：** 饿汉式单例
```java
class Singleton {
    //在类的内部是可以访问私有的结构
    private static Singleton INSTANCE = new Singleton();//内部产生实例化对象
    public static Singleton getInstance() {
        return INSTANCE;
    }
    private Singleton() {} //所有的方法都可以使用四种访问权限
    public void print() {
        System.out.println("Hello world !");
    }
}
```
饿汉式：构造方法私有化，只能通过static方法获取实例化对象。
懒汉式：当第一次去使用Singleton类对象时才会为其实例化的处理操作。

**范例：** 懒汉式单例
```java
class Singleton {
    //在类的内部是可以访问私有的结构
    private static Singleton instance;
    public static Singleton getInstance() {
        if(intance == null) {
            instance = new Singleton();
        }
        return instance;
    }
    private Singleton() {} //所有的方法都可以使用四种访问权限
    public void print() {
        System.out.println("Hello world !");
    }
}
```
## 多例设计（理解）

多例的对象的类实际上生活中也经常出现，例如：要求描述一周天数的类，只能有7个对象，描述性别的类只能有两个。多例只是比单例追加了更多个内部实例化对象。

**范例：** 定义一个表示性别的多例类
```java
class Gender {
    public static final int MALE_CMD = 1;
    public static final int FEMALE_CMD = 2;
    private static final Gender  MALE = new Gender("男");
    private static final Gender  FEMALE = new Gender("女");
    private String title;
    private Gender(String title) {
        this.title = title;
    }
    public static Gender getInstance(int ch) {
        switch(ch) {
            case MALE_CMD:
                return MALE;
            case FEMALE_CMD:
                return FEMALE;
            default:
                return null;
        }
    }
    public String toString() {
        return this.title;
    }
}
public class TestSingleton {
    public static void main(String[] args) {
        Gender s = null; //声明对象
        s = Gender.getInstance(Gender.MALE_CMD); //实例化对象
        System.out.println(s.toString());
    }
}
```
不管多例还是单例特点很明显：

- 构造方法私有化；
- 累内部一定会提供一个static方法用于取得实例化对象。