---
layout: post
title: "包装类"
categories: Java
tag: wrapper
author: "latestgo"
---

Object可以接收所有的应用数据类型，这其中又出现一个新问题，数据类型分为基本类型和引用类型，那基本数据类型怎么处理呢？

# 包装类的基本原理

所谓的包装类指的就是将基本数据类型封转到一个类中，如下的一个类：
```java
class MyInt {
	private int num;
	public MyInt(int num) {
		this.num = num;
	}
	public int intValue() {
		return this.num;
	}
}
```
这个时候的MyInt实际上就是一个int数据类型的包装类，利用MyInt可以实现基本数据类型变为对象的需求。
```java
public class TestDemo {
	public static void main(String[] args) {
		//子类对象自动变为Object父类对象
		Object obj = new MyInt(10);
		MyInt temp = (MyInt)obj;//去除内容向下转型
		System.out.print(temp.intValue() * 2);//20 //取出里面的基本数据类型进行操作
	}
}
```
**结论：** 将基本数据类型包装为一个类对象的本质在于方便的使用Object进行接收处理。Java中有8中基本数据类型。如果都按照以上的方式编写，就会存在问题：

- 所有的代码会重复
- 在进行数学计算的时候必须利用明确的方法将包装类中包装的基本数据类型取出后才可以进行

所以Java专门提供了包装类的使用，而对于包装类提供有两种类型：

- 对象型（Object直接子类）：Boolean、Character(char)；
- 数值型（Number直接子类）：Byte、Double、Short、Long、Integer(int)、Float。

**说明：** 关于Number类

- Number类的定义：`public abstract class Number extends Object implements Serializable`；
- 在Number类中的里面定义了6重要的方法。`byteValue()`, `doubleValue()`, `floatValue()`, `intValue()`, `longValue()`, `shortValue()`。

# 装箱和拆箱操作

在包装类与基本数据类型的处理之中有两类概念：

- 装箱：将基本数据类型变为包装类对象；

	- 利用每一个包装类提供的构造方法
- 拆箱：将包装类中包装的基本数据类型取出

	- 利用Number类中提供的xxValue()方法

**范例：** 以int和integer为例
```java
public class TestDemo {
	public static void main(String[] args) {
		Integer num = new Integer(10);//装箱
		int x = num.intValue(); //拆箱
		System.out.println(x * 2); //20
	}
}
```
**范例：** 以double和Double为例
```java
public class TestDemo {
	public static void main(String[] args) {
		Double num = new Double(10);//装箱
		double x = num.intValue(); //拆箱
		System.out.println(x * 2); //20.0
	}
}
```
以上的操作采用的手工的装箱和拆箱操作形式，而这种做法是在JDK1.5之前的做法，而从JDK1.5开始就提供有自动装箱和自动拆箱操的机制，最为重要的是，由于此类机制的存在，可以直接利用包装类的对象进行各种数学计算

**范例：** 自动装箱与自动拆箱
```java
public class TestDemo {
	public static void main(String[] args) {
		Integer x = 10; //自动装箱
		//这个时候发现可以直接利用包装类对象操作
		System.out.println(x + 2); //12
	}
}
```
但是这个时候依然会存在“==”和equals()问题。

**范例：** 观察问题
```java
public class TestDemo {
	public static void main(String[] args) {
		Integer num1 = 10; //自动装箱
		Integer num2 = 10; //自动装箱
		System.out.println(num1 == num2); //true
		System.out.println(num1 == new Integer(10));//false
		System.out.println(num1.equals(new Integer(10)));//true
	}
}
```
选择：使用int还是integer？

- 在接收的时候，使用的一定都是int，而保存数据的时候会使用Integer，Integer可以为null；
- 简单Java类统一不要再使用基本数据类型，都更换为包装类。

# 字符串与基本数据类型

以后要进行各种数据的输入，一定都是字符串类型的接受。如何将字符串变为各个基本数据类型，这个时候就需要包装类了。

- String变为int类型(Integer)：`public static int parseInt​(String s)`；
- String变为double类型(Double)：`public static double parseDouble​(String s)`；
- String变为boolean类型(Boolean)：`public static boolean parseBoolean​(String s)`。

**范例：** 将字符串变为int型
```java
public class TestDemo {
	public static void main(String[] args) {
		String str = "1234";
		int num = Integer.parseInt(str);
		System.out.println(num * 2); //2468
	}
}
```
**范例：** 将字符串变为double型
```java
public class TestDemo {
	public static void main(String[] args) {
		String str = "1234";
		double num = Double.parseDouble(str);
		System.out.println(num * 2); //2468.0
	}
}
```
但是特别需要引起主意的时候，将字符串变为数字的时候，字符串的组成有非数字，那么转换就将出现错误（`java.lang.NumberFormatException`），就因为这个异常，要写一堆的程序来回避它。

但是，字符串与boolean转换就不受多少局限了。

**范例：** 正常转换
```java
public class TestDemo {
	public static void main(String[] args) {
		String str = "true";
		String str1 = "dfafa";
		boolean num = Boolean.parseBoolean(str);
		boolean num1 = Boolean.parseBoolean(str1);
		System.out.println(num); //true
		System.out.println(num1);//false
	}
}
```
如果在进行boolean处理的时候发现字符串的组成不是true或者false，那么将统一按照false处理。

那么如果现在要想将基本数据类型变为字符串。

- 任何的数据类型使用了“+”连接空白字符串就变为字符串型；
	```java
	public class TestDemo {
		public static void main(String[] args) {
			String str = 100 + "";
			System.out.println(str.length()); //3
		}
	}
	```
- 使用String类中提供的`valueOf()`方法，这个方法可以进行转换，而且被重写了很多次。
	```java
	public class TestDemo {
		public static void main(String[] args) {
			String str = String.valueOf(100);
			System.out.println(str.length()); //3
		}
	}
	```
# 总结

字符串转为其他基本数据类型用的多。