---
layout: post
title: "继承的定义与使用"
categories: Java
tag: inherit
author: "latestgo"
---

面向对象的第二大特点就是继承：继承的主要作用在于已有的基础上继续进行功能的扩充。

# 继承问题的引出

如果要想更好的解释继承的作用，按照之前学习到的概念来定义两个类：人、学生。那么现在的定义效果如下：

- Person类

	```java
	class Person {
		private String name;
		private int age;
		public void setName(String name) {
			this.name = name;
		}
		public void setAge(int age) {
			this.age = age;
		}
		public String getName() {
			return this.name;
		}
		public int getAge() {
			return this.age;
		}
	}
	```
- Student类

	```java
	class Student {
		private String name;
		private int age;
		public String school;
		public void setName(String name) {
			this.name = name;
		}
		public void setAge(int age) {
			this.age = age;
		}
		public String getName() {
			return this.name;
		}
		public int getAge() {
			return this.age;
		}
		public void setSchool(String school) {
			this.school = school;
		}
		public String getSchool() {
			return this.school;
		}
	}
	```
以上的代码中是两个简单Java类，那么通过不断编写类似的就可以发现，程序之中会有重复的代码。

从代码上虽然重复了，但是现在概念上又可以发现，一个学生一定是人，但是学生和人相比学生更加具体。学生肯定描述的范围更小，具备的属性更多，方法也会更多。所以学生应该是一个人的扩充。

这个时候要想消除结构定义上的重复，就必须用继承来完成。

# 继承的实现

在Java之中继承使用`extends`关键字来实现，其定义的语法：`class 子类 extends 父类;`，子类也叫派生类，父类也叫超类。

**范例：** 继承的基本实现
```java
class Person {
	private String name;
	private int age;
	public void setName(String name) {
		this.name = name;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public String getName() {
		return this.name;
	}
	public int getAge() {
		return this.age;
	}
}
class Student extends Person {}
public class TestDemo {
	public static void main(String[] args) {
		Student stu = new Student(); //实例化子类
		stu.setName("I'm"); //此方法Student类没定义
		stu.setAge(20);
		System.out.println("姓名：" + stu.getName() + "，年龄：" + stu.getAge());
	}
} 
```
通过此时的代码可以发现，当发生了类继承关系之后，子类可以直接继承父类的操作，可以实现代码的重用。子类最低也维持和父类相同的功能。当然子类也可以进行功能的扩充，例如：扩充属性和方法。

**范例：** 子类功能的扩充
```java
class Person {
	private String name;
	private int age;
	public void setName(String name) {
		this.name = name;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public String getName() {
		return this.name;
	}
	public int getAge() {
		return this.age;
	}
}
class Student extends Person {
	public String school;
	public void setSchool(String school) {
		this.school = school;
	}
	public String getSchool() {
		return this.school;
	}
}
public class TestDemo {
	public static void main(String[] args) {
		Student stu = new Student(); //实例化子类
		stu.setName("I'm"); //此方法Student类没定义
		stu.setAge(20);
		stu.setSchool("MJUNI");
		System.out.println("姓名：" + stu.getName() + "，年龄：" + stu.getAge() + "，学校：" + stu.getSchool());
	}
} 
```

实现之后可以清楚地发现，继承可以进行父类功能的扩充，并且最重要的是可以重用父类中定义的方法。

# 继承的限制

虽然从本质上来讲继承的子类可以对父类的擦作进行共享，但是从另外一个角度来说，继承本身也是存在有一些限制的。 

1.  子类对象在进行实例化前一定会首先实例化父类对象，默认调用父类的构造方法后，再调用子类的构造方法进行子类对象实例化；

	```java
	class Person {
		public Person() {
			System.out.println("Person类对象创建");
		}
	}
	class Student extends Person {
		public Student() {
			//既然是要进行构造方法的调用，那么构造方法的调用一定要放在构造方法的首行
			super(); //此语句在无参时写与不写一样
			System.out.println("Student类对象创建");
		}
	}
	public class MyDemo {
		public static void main(String[] args) {
			new Student(); //进行子类对象实例化
		}
	}
	```

	没有一条语句会明确调用父类构造，但是父类构造被执行了。所以子类对象的实例化一定会实例化其父类对象。

	但是需要注意，实际上这个时候在子类的构造方法中相当于隐含了一句语句“`super()`”。

	同时需要注意的是，如果此时父类中没有提供有无参构造，那么就必须使用`super()`明确的指明需要调用的父类构造方法。

	```java
	class Person {
		public Person(String name, int age) {
			System.out.println("Person类对象创建");
		}
	}
	class Student extends Person {
		public Student(String name, int age) {
			super(name, age); //明确定义要调用的父类构造
			System.out.println("Student类对象创建");
		}
	}
	public class MyDemo {
		public static void main(String[] args) {
			new Student("CSDN", 22); //进行子类对象实例化
		}
	}
	```

2. Java中只允许单继承，不允许多继承；

	一个子类只能继承一个父类，若同时需要拥有两个类的属性和操作，那么可以采用多层继承的形式完成。

3. 在继承的时候，子类会继承父类的所有结构，但是需要注意非私有操作属于显示继承（直接调用），而所有的私有操作属于隐式继承（通过其他形式调用，例如：`setter`，`getter`）。

	父类中的属性的确被子类所继承了，而子类能够使用的只是所有的非`private`操作（`public`），所有的`private`无法直接使用，所以称为隐式继承。

# 总结

1. 继承的语法以及继承的目的；
2. 子类对象的实例化流程：一定是先实例化父类对象；
3. 不允许多重继承，允许多层继承。
