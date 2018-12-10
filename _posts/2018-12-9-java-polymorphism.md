---
layout: post
title: "多态性"
categories: Java
tag: 多态
author: "latestgo"
---

知道了关于继承的所有概念，但是并不意味着只要使用继承就可以实现代码的全部重用，而在继承之后又有了一个重要的概念：多态性。

在Java里面对于多态的核心的表现主要有以下两点：

- 方法的多态性：

	- 方法的重载：同一个方法名称可以根据参数的类型及个数的不同调用不同的方法体；
	- 方法的重写：同一个父类的方法，可能根据实例化的子类不同也有不同的实现。
- 对象的多态性（前提：方法重写）：

	- 【自动】对象的向上转型：父类 父类对象 = 子类实例；
	- 【强制】对象的向下转型：子类 子类对象 = (子类)父类实例。

**范例：** 一个简单程序
```java
class A {
	public void print() {
		System.out.println("【A】public void print(){}");
	}
}
class B extends A {
	public void print() {
		System.out.println("【B】public void print(){}");
	}
}
public class TestDemo {
	public static void main(String[] args) {
		B b = new B(); //实例化子类对象
		b.print(); //调用被重写的方法
	}
} 
```

可以将以上的代码进一步变化，变为向上转型。

**实例：** 向上转型
```java
public class TestDemo {
	public static void main(String[] args) {
		A a = new B(); //实例化子类对象
		a.print(); //调用被重写的方法
	}
} 
```
不管是否发生了向上转型，那么其核心的本质还是在于：哪一个子类（new在哪里），而后调用的方法是否被子类所重写了。

向下转型指的是父类对象变为子类对象，但是在这之前需要知道一个核心概念，为什么需要向下转型？当需要使用到子类扩充操作的时候就要采用向下转型。

**范例：** 向下转型
```java
}
public class TestDemo {
	public static void main(String[] args) {
		A a = new B(); //向上转型
		a.print();
		//这个时候父类能够调用的方法只能是自己本类定义好的
		//并没有B类中的funB()，那么只能够进行向下转型
		B b = (B)a;
		b.funB(); 
	}
} 
```
但是并不是所有的父类对象都可以向下转型：如果要进行向下转型操作之前，一定要首先发生向上转型，否则在转型时会出现：`ClassCastException`。

**范例：** 错误的转型
```java
public class TestDemo {
	public static void main(String[] args) {
		A a = new A(); //实例化父类对象
		B b = (B) a; //强制转换 ClassCastException: A cannot be cast to B
	}
} 
```

但是现在有一个问题出现了，如果向下转型可能存在有隐患，那么如何转型才能靠谱呢？最好的做法是先进行判断，而后才进行转型，那么就可以依靠`instanceof`关键字来实现，此关键字的使用语法：`子类对象 instanceof 类`，返回的是boolean型数据。

**范例：** instanceof的使用
```java
public class TestDemo {
	public static void main(String[] args) {
		A a = new B();
		System.out.println(a instanceof A);
		System.out.println(a instanceof B);
		//对子类的特殊操作尽量慎用
		if(a instanceof B) {  //避免ClassCastException
			B b = (B)a;
			b.funB();
		}
	}
} 
```

清楚了向上向下转型，还需思考这种转换有什么意义？

**范例：** 定义一个方法，这个方法可以接受Person类所有子类实例，并调用Person类的方法
```java
class Person {
	public void takeoff() {
		System.out.println("take off");
	}
}
class Student extends Person {
	public void takeoff() {
		System.out.println("take off one by one");
	}
}
class Worker extends Person {
	public void takeoff () {
		System.out.println("take off all once");
	}
}
public class TestDemo {
	public static void main(String[] args) {
		in(new Student());
		in(new Worker());
	}
	public static void in(Person per) {
		per.takeoff(); //所有的人不管什么类别都按照统一的方法takeoff
	}
} 
```

通过以上的分析可以清楚，对象的向上转型有一个核心用途：操作参数统一。

# 总结
1. 对象多态性实现的核心在于方法重写；
2. 对象的向上转型可以接受参数的统一，而向下转型可以实现扩充方法的调用。
3. 两个没有关系的类对象不能够进行转型的，会发生`ClassCastException`，所以向下转型是存在安全隐患。