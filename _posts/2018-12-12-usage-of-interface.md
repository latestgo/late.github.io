---
layout: post
title: "接口的定义与使用"
categories: Java
tag: interface
author: "latestgo"
---

抽线类与普通类相比最大的特点是约定了子类的实现要求，但是抽象类有一个缺点：单继承，如果要想约定子类的实现要求以及避免单继承的局限就需要使用接口。设计中可以使用抽象类和接口，接口优先选择。

# 接口的基本概念

接口就是一个抽象方法和全部常量的集合，在Java中可以使用`interface`关键字来进行定义。

**范例：** 定义一个接口
```java
//因为接口和类的定义命名要求相同，所以为了区分
//建议在接口前面追加一个字母I
interface IMessage {
	public static final String MSG = "blog.isohard.cn";
	public abstract void print();
}
```
如果子类要使用接口，那么利用`implements`关键字来实现接口，同时一个子类可以实现多个接口，也就是利用接口实现多继承的概念，对于接口的子类（如果不是抽象类）则必须重写接口中的全部抽象方法，随后可以利用子类的向上转型通过实例化子类来得到接口的实例化对象。

```java
interface IMessage {
	public static final String MSG = "blog.isohard.cn";
	public abstract void print();
}
interface INews {
	public abstract String get();
}
//一个类现在可以实现多个接口
class MessageImpl implements IMessage, INews {
	public void print() {
		System.out.println(IMessage.MSG);
	}
	public String get() {
		return IMessage.MSG; //访问常量都建议加上类名称
	}
}
public class TestDemo {
	public static void main(String[] args) {
		IMessage m = new MessageImpl(); //子类为父接口实例化
		m.print();
	}
} 
```
当一个子类继承了多个接口之后，并且接口对象通过子类进行实例化，那么多个父接口之间允许互相转换。
![![IMG_0008.jpg](assets\IMG_0008.jpg)](https://img-blog.csdnimg.cn/20181211214533898.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)

# 接口的使用限制

接口一旦定义完成之后就需要对其有一个核心的说明，首先需要说明的是：接口里面只允许存在有public权限，也就是说不管是属性还是方法其权限永远都是public。

**范例：** 错误的重写
```java
interface INews {
	abstract String get(); //即使不谢public，也是public
}
class NewsImpl implements INews {
	String get () {//权限更加严格了，所以无法重写
		return "blog.isohard.cn";
	}
}
```
所以方法尽量使用public定义。

另外优于接口之中只是全局常量和抽象方法的集合，所以一下的两种的定义形式的效果都是一样的。
```
interface INews {
	public static final String MSG = "blog.isohard.cn";
	public abstract String get(); 
}
```
```
interface INews {
	String MSG = "blog.isohard.cn";
	String get(); 
} 
```
在编写接口的时候，基本上接口里面只会提供抽象方法，很少有许多的全局常量。所以为了避免出现混乱，接口的方法往往都要加上public。

当一个子类需要实现接口又需要继承抽象类的时候，先使用`extends`继承一个抽象类，在使用`implements`实现多个接口。

**范例：** 子类继承抽象类和实现接口
```java
interface INews {
	public String get(); 
}
abstract class AbstractMessage {//抽象类命名一般在前加上Abstract
	public abstract void print();
}
class NewsImpl extends AbstractMessage implements INews {
	public String get() {
		return "isohard.cn";
	}
	public void print(){}
}
public class TestDemo {
	public static void main(String[] args) {
		INews news = new NewsImpl();
		System.out.println(news.get());
		// NewsImpl 是抽象类和借口的共同子类
		AbstractMessage am = (AbstractMessage)news;
		am.print();
	}
} 
```
一个抽象类可以使用`implements`实现多个接口，但是接口不能去继承抽象类。
```java
interface INews {
	public String get(); 
}
abstract class AbstractMessage implements INews {
	public abstract void print();
}
class NewsImpl extends AbstractMessage{
	public String get() {
		return "isohard.cn";
	}
	public void print(){}
}
```
上面代码是三层的继承关系。
```java
interface INews {
	public String get();
	public void print();
}
//假设一个接口可能有无数个子类，但是对于一个方法的实现是一样的。
abstract class AbstractMessage implements INews {
	public void print() {
		System.out.println("blog.isohard.cn");
	}
}
//只是做个重复的标记，便于知道NewsIple是INews的子类
class NewsImpl extends AbstractMessage implements INews{
	public String get() {
		return "isohard.cn";
	}
}
```
![![PNG image 3.png](assets\PNG image 3.png)](https://img-blog.csdnimg.cn/20181211230043499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)

一个接口可以使用`extends`来继承多个接口。
```java
interface A{
	public void printA();
}
interface B {
	public void printB();
}
interface C extends A, B {
	public void printC();
}
class Impl implements C {
	public void printA() {}
	public void printB() {}
	public void printC() {}
}
```
接口可以定义一系列内部接口，包括：内部普通类、内部抽象类、内部接口，其中static定义的接口相当于定义一个外部接口。
```java
interface A{
	static interface B { //使用了static定义，描述了一个外部接口
		public void printB();
	}
}
class X implements A.B {
	public void printB() {}
}
```
对于内部的接口不是首选。

# 接口的应用：定义标准

对于接口在实际的开发之中有三大核心应用：

- 定义操作标准
- 表示能力
- 在分布式之中暴露远程服务方法

先假如一个概念：电脑上可以使用任何的USB设备（U盘、打印机）。
![PNG image 4.png](https://img-blog.csdnimg.cn/20181213224349958.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)

**范例：** 定义USB标准
```java
interface USB {
	public void setup();//按照USB驱动
	public void work();//进行工作
}
```
**范例：** 定义电脑类
```java
class Computer {
	public void plugin(USB usb) {//只能插USB设备
		usb.setup();//安装
		usb.work();//工作
	}
}
```
电脑在生产时是不关注USB设备有哪些的。

**范例：** 定义USB子类
```java
class Flash implements USB {
	public void setup() {
		System.out.println("安装U盘驱动");
	}
	public void work() {
		System.out.println("进行数据传输");
	}
}
```

**范例：** 定义打印机子类
```java
class Printer implements USB {
	public void setup() {
		System.out.println("安装打印机驱动");
	}
	public void work() {
		System.out.println("进行文件打印");
	}
}
```
**范例：** 测试类
```java
public class TestDemo {
	public static void main(String[] args) {
		Computer com = new Computer();
		com.plugin(new Flash());
		com.plugin(new Printer());
	}
} 
```
发现使用了接口和对象的多态性的概念结合之后，对于参数的统一更加明确了。而且可以发现接口是在类之上的设计抽象。
![PNG image 5.png](https://img-blog.csdnimg.cn/2018121323081667.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)

# 接口的设计模式——工厂设计模式（Factory）
一个简单的程序范例：在进行类设计的时候，要求首先需要有接口，而后接口要通过子类才可以进行对象的实例化处理。

**范例：** 传统程序开发
```java
interface IFruit {//定义水果的操作
	public void eat();//吃
}
class Apple implements IFruit {
	public void eat() {
		System.out.print("削皮吃苹果");
	}
}
public class TestDemo {
	public static void main(String[] args) {
		//子类为接口进行对象的实例化处理
		IFruit fruit = new Apple();
		fruit.eat();
	}
} 
```
此时的程序实现的关键是：`IFruit fruit = new Apple();`，如果没有此语句，那么接口对象将无法进行实例化的操作处理。但是最大的败笔也在此，主方法是一个客户端，那么对程序的修改不应该影响到客户端。

Java实现可移植的关键是JVM，也就是说所有的程序在JVM上执行，而不同的操作系统有匹配的JVM，那么相当于程序 ——>JVM ——>操作系统。

这个时候new是整个开发过程之中最大的耦合元凶，而在开发之中要想进行解耦合的关键就在于要引入第三方，所以这个类可以使用`Factory`来描述。
**范例：** 通过Factory设计来描述
```java
interface IFruit {//定义水果的操作
	public void eat();//吃
}
class Factory {
	//因为此时Factory产生实例化对象没有意义
	public static IFruit getInstance(String className) {
		if("apple".equals(className)) {
			return new Apple();
		}
		if("orange".equals(className)) {
			return new Orange();
		}
		return null;
	}
}
class Apple implements IFruit {
	public void eat() {
		System.out.print("削皮吃苹果");
	}
}
class Orange implements IFruit {
	public void eat() {
		System.out.print("剥皮吃橘子");
	}
}
public class TestDemo {
	public static void main(String[] args) {
		if(args.length != 1) {
			System.out.println("应当跟一个类名称的参数");
			System.exit(1);
		}
		IFruit fruit = Factory.getInstance(args[0]);
		fruit.eat();
	}
} 
```
当更换使用的IFruit子类的时候主方法没有任何主方法没有任何变化就可以实现了子类的变更，这样的设计就是工厂设计模式。
**总结：** 编写的接口如果要想取得接口的实例化对象，第一反应：写工厂类。

# 接口设计设计模式：代理设计模式（Proxy）

所谓的代理严格来讲就是两个字类共同实现一个接口，其中一个子类负责真实的业务实现，而另外的子类负责辅助真实业务主题的操作。
![PNG image 6.png](https://img-blog.csdnimg.cn/20181214005518186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)
**范例：** 实现代理设计
```java
interface ISubject {
	public void save(); //核心功能是救人
}
class RealSubject implements ISubject {
	public void save() {
		System.out.println("英勇地制止了于先生马上进行的不法侵害。");
	}
}
class ProxySubject implements ISubject{//代理实现
	private ISubject subject; //真正的操作业务
	//创建代理类对象的时候必须设置要代理的真实主题
	public ProxySubject(ISubject subject) {
		this.subject = subject;
	}
	public void broke() {
		System.out.println("1、破门而入");
	}
	public void get() {
		System.out.println("2、得到见义勇为奖");
	}
	public  void save() {//接口子类一定要实现抽象方法
		this.broke(); //真实操作前的准备
		this.subject.save(); //调用真实的业务
		this.get();//操作后的收尾
	}
}
class Orange implements IFruit {
	public void eat() {
		System.out.print("剥皮吃橘子");
	}
}
class Factory {
	public static ISubject getInstance() {
		return new ProxySubject(new RealSubject());
	}
}
public class TestDemo {
	public static void main(String[] args) {
		ISubject sub = Factory.getInstance();
		//通过代理类对象发出，利用代理类来实现真实业务调用
		sub.save();//救人的核心操作
	}
} 
```
对于初次接触代理设计模式有点抽象。代理的本质：所有的真实业务操作都会有一个与之辅助的功能类共同完成。

# 抽象类的区别

抽象类和接口都属于常用的类结构设计，在开发之中都会出现，按照优先选择来讲，接口一定要比抽象类更方便。从概念上来将两者对比：
| No | 区别 | 抽象类 |接口 |
| --- | --- | --- | --- |
| 1 | 关键字 | abstract class 类名称 {} | interface 接口名称 {} |
| 2 | 结构组成 | 抽象方法、普通方法、全局常量、全局变量、属性、构造方法 | 抽象方法和全局常量 |
| 3 | 权限 | 可以使用各种权限 | 只能使用public访问权限 |
| 4 | 子类使用 | 子类利用extends关键字类继承抽象类 | 子类利用implements来实现接口 |
| 5 | 关系 | 一个抽象类可以实现若干个接口 | 一个接口不能继承抽象类，但是可以使用extends继承多个接口 |
| 6 | 子类限制 | 一个字累只能够继承一个抽象类 | 一个子类可以实现多个接口 |

除了单继承的局限之外，实际上使用抽象类和接口都是类似的，但是实际上，抽象类的设计要比接口复杂。要求掌握如何定义接口以及更好的实现子类。

对于现在的结构体很多：类、对象、抽象类、接口，这几者的关系如下：

![PNG image 7.png](https://img-blog.csdnimg.cn/2018121421301498.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21yYmFja2Vy,size_16,color_FFFFFF,t_70)

# 总结

1. 接口是Java的核心
2. 优先考虑接口，避免单继承局限