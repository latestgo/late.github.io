---
layout: post
title: "Object类"
categories: Java
tag: Object
author: "latestgo"
---

Object类是Java默认提供的一个类，可以这么说，Java里面除了Object类之外，所有的类都是存在有继承关系的。默认会继承Object父类，也就是说以下两种类定义是完全相同的。

> class Message {}
> class Message extends Object {}

那么也就是证明所有的类对象都可以使用Object接收。
```java
class Message {}
class Person {}
public class TestDemo {
	public static void main(String[] args) {
		fun(new Message());
		fun(new Person());
	}
	public static void fun(Object obj) {//表示所有的类对象都是接收
		System.out.println(obj);
	}
} 
```
所以在开发之中Object类是参数的最高统一类型。但是Object类本身也具备有一些定义的的方法。
| No | 方法名称 | 类型 | 描述 |
| --- | --- | --- | --- |
| 1 | public Object() | 构造 | 无参构造是专门为子类提供服务的 |
| 2 | public String toString() | 普通 | 取得对象信息 |
| 3 | public boolean equals​(Object obj) | 普通 | 对象比较 |
对于整个Object类中方法都需要熟悉。

# 取得对象信息

在使用对象直接输出的时候，默认情况是输出一个地址编码。但是如果使用String类时，输出的就是内容，主要的原因就是`toString()`方法问题。
**范例：** String对象输出
```java
class Person {
	private String name;
	private int age;
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
}
public class TestDemo {
	public static void main(String[] args) {
		fun("Hello"); //String是Object子类  Hello
		fun(new Person("zhangsan", 10)); //Person@659e0bfd
	}
	public static void fun(Object obj) {//表示所有的类对象都是接收
		System.out.println(obj.toString()); //默认输出对象就是调用toString()
	}
} 
```
默认情况下Object类中提供的`toString()`方法只能够得到一个对象的地址（这是所有对象都共有具备的特征）。而如果默认的toString()功能不足，可以在需要的子类中重写toString()方法。
**范例：** 重写toString()方法
```java
class Person {
	private String name;
	private int age;
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
	public String toString() {//重写Object类中的方法
		return "姓名：" + this.name + "，年龄：" + this.age;
	}
}
public class TestDemo {
	public static void main(String[] args) {
		fun("Hello"); //String是Object子类  Hello
		fun(new Person("zhangsan", 10)); //姓名：zhangsan，年龄：10
	}
	public static void fun(Object obj) {//表示所有的类对象都是接收
		System.out.println(obj.toString()); //默认输出对象就是调用toString()
	}
} 
```
toString()的核心目的在于取得对象信息。相当于替换了之前简单Java类中的getInfo()方法的功能。

String是作为信息输出的重要的数据类型，在Java中所有的数据类型只要遇见了String，并且执行了“+”的连接操作，那么都要求**将其变为字符串**后连接，而所有的对象要变为字符串就默认使用toString()方法。
```java
class Person {
	private String name;
	private int age;
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
	public String toString() {//重写Object类中的方法
		return "姓名：" + this.name + "，年龄：" + this.age;
	}
}
public class TestDemo {
	public static void main(String[] args) {
		String result = "Hello" + new Person("zhangsan", 10);
		System.out.println(result); //Hello姓名：zhangsan，年龄：10
	}
} 
```
# 对象比较

String类内容的比较使用的是equals()方法，实际上这个equals()方法就是String类重写了Object类的方法。
> String类中equals()：public boolean equals​(Object anObject);

**范例：** 实现对象比较
```java
class Person {
	private String name;
	private int age;
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
	public boolean equals(Object anObject) {
		if(anObject == null) {
			return false;
		}
		if(this == anObject) {
			return true;
		}
		if(!(anObject instanceof Person)) {
			return false;
		}
		//必须将Object类型变为Person类型后才可以调用name和age属性
		Person per = (Person)anObject;
		return this.name.equals(per.name) && this.age == per.age;
	}
	public String toString() {//重写Object类中的方法
		return "姓名：" + this.name + "，年龄：" + this.age;
	}
}
public class TestDemo {
	public static void main(String[] args) {
		Person per1 = new Person("zhangsan", 10);
		Person per2 = new Person("zhangsan", 10);
		System.out.println(per1.equals(per2)); //true
		System.out.println(per1.equals("Hello")); //false
	}
} 
```
需要注意一个对象比较的形式：
`public boolean equals(Person anObject){}`
因为父类中equals()方法用的是Object，所以以上的方法严格来讲不叫重写了，叫重载。

# Object接收引用数据类型

Object可以接收任意的对象，因为Object是所有类的父类，但是Object的概念不仅仅局限于此，它可以接收所有的引用数据类型，包括：数据、接口。
**范例：** 使用Object接收数组对象
```java
public class TestDemo {
	public static void main(String[] args) {
		//利用Object接收数组对象，向上转型
		Object obj = new int[]{1, 2, 3};
		//向下转型，需要强制转型
		int[] data = (int[])obj;
		for(int x = 0; x < data.length; x++) {
			System.out.print(data[x] + "、"); //1、2、3、
		}
	}
} 
```
而Object可以接受接口更是Java中的强制性要求，因为接口本身是不可能继承任何类的，所以这种的类型的接收是自己的规定。
**范例：** 使用Object接收接口对象
```java
interface IMessage {}
class MessageImpl implements IMessage {
	public String toString() { //Object类的方法
		return "blog.isohard.cn";
	}
}
public class TestDemo {
	public static void main(String[] args) {
		IMessage msg = new MessageImpl(); //子类向父接口转型
		Object obj = msg; //接口向Object转型
		System.out.println(msg.toString());//blog.isohard.cn
		IMessage tmp = (IMessage)obj;
	}
} 
```
Object真正达到了参数统一，如果一个类希望接收所有的数据类型，就是用Object完成。