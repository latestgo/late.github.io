---
layout: post
title: "抽象类的定义与使用"
categories: Java
tag: abstract
author: "latestgo"
---

在以后所有的进行的项目开发之中，不要出现一个类去继承一个已经实现好的类，而只能继承抽象类和接口。

对象多态性的核心本质在于方法的重写，那么如果现在子类没有去进行指定方法的重写，这样的操作就有些不合要求的。所以如果要对子类的方法进行一些强制的要求就必须采用抽象类来解决。

# 抽象类的基本概念

抽象类只是在普通类的基础上扩充了一些抽象方法而已。所谓的额抽象方法指的是只声明而未实现（没有方法体）的方法，所有的抽象方法要求使用`abstract`关键字定义，并且抽象方法所在的类也一定要使用`abstract`定义类，表示抽象类。

**范例：** 定义一个抽象类
```java
abstract class A {
	private String msg = "blog.isohard.cn";
	public void print() {
		System.out.println(msg);
	}
	//抽象方法不含方法体
	public abstract void fun() {}
}
```
抽象类就是比普通方法多一些抽象方法。

抽象类中包含抽象方法，抽象方法最大的区别在于其没有方法体，即：不知道具体的实现，而如果此时产生了实例化对象，则意味着可以调用类中的所有操作。

抽象类的使用原则：

- 所有的抽象类必须要有子类；
- 抽象的子类（不是抽象类）必须重写抽象方法；

	- 方法重写一定要考虑到权限问题：抽象方法可以使用任意权限，要求权限尽量都用public。

- 抽象的对象可以通过对象多态性利用子类为其实例化。

**范例：** 使用抽象类
```java
abstract class A {
	private String msg = "blog.isohard.cn";
	public void print() {
		System.out.println(msg);
	}
	//抽象方法不含方法体
	public abstract void fun();
}
//一个子类只能够通过extends来继承抽象类，所以依然单继承
class B extends A {
	public void fun() {
		System.out.println("Hello World");
	}
}
public class TestDemo {
	public static void main(String[] args) {
		A a = new B();
		a.fun();
	}
} 
```

还有一种使用形式：
```java
abstract class A {
	private String msg = "blog.isohard.cn";
	public void print() {
		System.out.println(msg);
	}
	public static A getInstance() {
		class B extends A {
			public void fun() {
				System.out.println("Hello World");
			}
		}
		return new B();
	}
	//抽象方法不含方法体
	public abstract void fun();
}
public class TestDemo {
	public static void main(String[] args) {
		A a = A.getInstance();
		a.fun();
	}
} 
```
使用的是内部类，对于封装性很有好处。

# 抽象类的相关规定

抽象类只是比普通类多了一些抽象方法的定义而已，所以在抽象类之中依然允许提供有构造方法，并且子类也会遵守子类对象的实例化流程。实例化子类对象前一定要先去调用父类的构造方法。

**范例：** 在抽象类中定义构造方法
```java
abstract class Person {
	private String name;
	private int age;
	public Person() {
		System.out.println("*****");
	}
	public abstract String getInfo();
}
class Student extends Person {
	private String school;
	public Student() {
		System.out.println("#####");
	}
	public String getInfo() {
		return null;
	}
}
public class TestDemo {
	public static void main(String[] args) {
		new Student();
	}
} 
```

如果父类中没有无参构造，那么子类必须通过`super()`调用父类的构造方法。

其实抽象类中存在构造方法，毕竟抽象类中还有属性，所有的属性一定要在对象实例化的时候进行空间的开辟，对象实例化必须要使用构造方法。

关于对象实例化的核心：

- 进行类的加载；
- 进行类对象的空间开辟；
- 进行类对象中的属性初始化（构造方法）。

```java
abstract class A {
	public A() { //4. 调用父类构造方法
		this.print(); //5. 调用被子类重写的方法
	}
	public abstract void print();
}
class B extends A {
	private int num = 100;
	public B(int num) { //2. 调用子类实例化对象
		super(); // 3.隐含一行语句，实际要想调用父类构造方法
		this.num = num; //7.为类中的属性初始化
	}
	public void print() { //6.此时子类对象的属性还有被初始化
		System.out.print(this.num);//7.内容是其对应数据类型的默认值
	}
}
public class TestDemo {
	public static void main(String[] args) {
		new B(30); //实例化子类对象
	}
} 
```
结论：如果构造方法没有执行，那么对象
中的属性一定都是其对应数据类型的默认值。

抽象类中可以不定义任何的抽象方法，但是此时抽象类依然无法直接实例化。

抽象类一定不能使用final进行声明，因为使用final定义的类不能够有子类，而抽象类必须有子类。抽象方法不能使用private定义，抽象方法一定要被子类重写。

抽象类也分为内部抽象类和外部抽象类，内部抽象类中可以使用static定义，描述为外部抽象类。

**范例：** 内部抽象类
```java
abstract class A {
	public abstract void printA();
	abstract class B {
		public abstract void printB();
	}
}
class X extends A {
	public void printA() {}
	class Y extends B {
		public void printB(){};
	}
}
```

如果外部抽象类使用了static就是语法错误，可以内部抽象类上允许使用static。
```java
abstract class A {
	public abstract void printA();
	static abstract class B {
		public abstract void printB();
	}
}
class X extends A.B {
	public void printB() {}
}
```
# 抽象实际应用 —— 模版设计模式

抽象类最大的特点在于强制规定了子类的实现结构，除了这样一特点，抽象类还可以起到模版的作用，下面做一个简单地分析：

- 人 = 吃饭 + 睡觉 + 工作；
- 猪 = 吃饭 + 睡觉
- 机器人 = 吃饭 + 工作。

那么现在假设有一个按钮控制（方法），一旦传入了某些指令后就可以进行相应的处理。

**范例：** 描述抽象行为
```java
abstract class Action {
	public static final int EAT = 1;
	public static final int SLEEP = 5;
	public static final int WORK = 10;
	public void command() {
		switch(cmd) {
			case EAT:
				this.eat();
				break;
			case SLEEP:
				this.sleep();
				break;
			case WORK:
				this.work();
				break;
			case EAT + SLEEP + WORK:
				this.eat();
				this.sleep();
				this.work();
				break;
		}
	}
	public abstract void eat();
	public abstract void sleep();
	public abstract void worker();
}
```
![PNG image 2.png](https://img-blog.csdnimg.cn/2018121120482094.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)

**范例：** 定义各个行为的子类
```java
class Human extends Action {
	public void eat() {
		System.out.println("人吃饭");
	}
	public void sleep() {
		System.out.println("人睡觉");
	}
	public void work() {
		System.out.println("人工作");
	}
}
class Pig extends Action {
	public void eat() {
		System.out.println("猪吃食物");
	}
	public void sleep() {
		System.out.println("猪圈睡觉");
	}
	public void work() {}
}
class Robot extends Action {
	public void eat() {
		System.out.println("机器充电");
	}
	public void sleep() {}
	public void work() {
		System.out.println("机器不停工作");
	}
}
```

**范例：** 调用各自行为的操作
```java
public class TestDemo {
	public static void main(String[] args) {
		fun(new Human());
		fun(new Pig());
		fun(new Robot());
	}
	public static void fun(Action action) {
		action.command(Action.EAT + Action.SLEEP + Action.WORK);
	}
} 
```

实际上通过此程序的定义结构可以清楚：

- 抽象类在实际使用过程之中会定义固话的模式，它只能接受特定的指令；
- 但是每周指令的具体实现由子类负责完成，父类只做了方法的约定。

# 总结

1. 抽象类虽然定义了子类必须要做的事情，但是抽象类依然有单继承的限制；
2. 抽象类的使用必须通过子类进行对象实例化处理。