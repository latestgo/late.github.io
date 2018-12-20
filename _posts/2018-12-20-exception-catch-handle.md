---
layout: post
title: "异常的捕获和处理"
categories: Java
tag: Exception
author: "latestgo"
---

几乎所有的代码都可能产生异常，所以为了保证程序出现异常之后可以正常执行完毕，就需要进行异常处理。

# 异常的影响

异常是导致程序中断执行的指令流。但是程序之中如果出现异常并且没有合理处理的话，就会导致程序终止执行。

**范例：** 没有异常产生的程序
```java
public class TestDemo {
	public static void main(String[] args) {	
		System.out.println("【1】数学计算开始前");//【1】数学计算开始前
		System.out.println("【2】数学计算" + (10 / 2));//【2】数学计算5
		System.out.println("【3】数学计算结束后");//【3】数学计算结束后
	}
}
```
如果此时没有任何的异常产生，那么程序可以正常执行完毕。

**范例：** 产生异常
```java
public class TestDemo {
	public static void main(String[] args) {	
		System.out.println("【1】数学计算开始前");
		System.out.println("【2】数学计算" + (10 / 0));

System.out.println("【3】数学计算结束后");
	}
}
```
>【1】数学计算开始前
>Exception in thread "main" java.lang.ArithmeticException: / by zero
> at TestDemo.main(TestDemo.java:4)

现在的程序产生了异常，但是在异常语句之前的代码依然可以正常执行，而异常产生之后，程序直接进行结束。为了保证程序出现异常后还可以继续向下执行，就需要进行异常处理。

# 异常的处理

为了保证程序出现了错误之后依然可以正常执行，那么就可以使用异常处理操作，处理语法如下：
```java
try {
	有可能出现异常的语句;
} [catch(异常类 对象) {
	异常处理语句;
} catch(异常类 对象) {
	异常处理语句;
}...] [finally {
	异常统一出口;
}]

```
对于以上的三个关键字，可以出现的组合：try..catch、try..finally、try..catch..finally。

**范例：** 对异常进行处理
```java
public class TestDemo {
	public static void main(String[] args) {	
		System.out.println("【1】数学计算开始前");//【1】数学计算开始前
		try {
			System.out.println("【2】数学计算" + (10 / 0));
		} catch(ArithmeticException e) {
			System.out.println("【catch】异常已被处理"); //【catch】异常已被处理
		}
		System.out.println("【3】数学计算结束后");//【3】数学计算结束后

	}
}
```

出现了异常后，程序由于存在有异常的处理机制，那么依然可以执行完毕。以上代码虽然进行了异常的处理，但是这里面会存在一个问题，就是自己不会知道程序到底产生了什么样的异常，所以为了明确取得异常的信息，可以直接输出异常类对象，或者调用所有异常类提供的`printStackTrace()`方法进行完整异常信息输出。
```java
public class TestDemo {
	public static void main(String[] args) {	
		System.out.println("【1】数学计算开始前");//【1】数学计算开始前
		try {
			System.out.println("【2】数学计算" + (10 / 0));
		} catch(ArithmeticException e) {
			e.printStackTrace(); //java.lang.ArithmeticException: / by zero
			//at TestDemo.main(TestDemo.java:5)
			//System.out.println("【catch】异常已被处理" + e); //【catch】异常已被处理java.lang.ArithmeticException: / by zero
		}
		System.out.println("【3】数学计算结束后");//【3】数学计算结束后

	}
}
```

在进行异常处理的时候还可以使用try..catch..finally来操作。

**范例：** try..catch..finally
```java
public class TestDemo {
	public static void main(String[] args) {	
		System.out.println("【1】数学计算开始前");//【1】数学计算开始前
		try {
			System.out.println("【2】数学计算" + (10 / 0));
		} catch(ArithmeticException e) {
			e.printStackTrace(); //java.lang.ArithmeticException: / by zero
			//at TestDemo.main(TestDemo.java:5)
			//System.out.println("【catch】异常已被处理" + e); //【catch】异常已被处理java.lang.ArithmeticException: / by zero
		} finally {
			System.out.println("【FINALLY】不管有没有异常都要执行"); //【FINALLY】不管有没有异常都要执行
		}
		System.out.println("【3】数学计算结束后");//【3】数学计算结束后
	}
}
```
不管此时是否产生有异常，最终都要执行finally，finally作为程序的统一出口。

以上程序是直接固定好两个数字进行除法计算，现在希望通过初始化参数来进行除法计算。

**范例：**  接收参数进行计算
```java
public class TestDemo {
	public static void main(String[] args) {	
		System.out.println("【1】数学计算开始前");//【1】数学计算开始前
		try {
			int x = Integer.parseInt(args[0]);
			int y = Integer.parseInt(args[1]);
			System.out.println("【2】数学计算" + (x / y));
		} catch(ArithmeticException e) {
			e.printStackTrace(); //
		} finally {
			System.out.println("【FINALLY】不管有没有异常都要执行"); //【FINALLY】不管有没有异常都要执行
		}
		System.out.println("【3】数学计算结束后");//【3】数学计算结束后
	}
}
```
有可能存在如下的问题：

- 执行时没有输入参数：`ArrayIndexOutOfBoundsException`
- 输入不是数字：`NumberFormatException`
- 被除数为0：`ArithmeticException`

在进行catch捕获异常的时候，如果没有捕获指定的异常，那么程序依然无法进行处理，所以最直白的解决方案就是使用多个catch。

# throws关键字

在进行方法的定义的的时候，如果要告诉调用者本方法可能产生哪些异常，就可以使用throws进行声明，如果该方法出现问题后不希望进行处理，就是用throws抛出。

**范例：** 使用throws定义方法
```java
class MyMath {
	//此处明确声明该方法上会有异常
	public static int div(int x, int y) throws Exception {
		return x / y;
	}
}
public class TestDemo {
	public static void main(String[] args) {
		try {
			System.out.println(MyMath.div(10, 2));
		} catch(Exception e) {
			e.printStackTrace();
		}
	}
}
```
如果使用throws声明的方法，那么在调用时就必须使用try..catch进行异常捕获，因为该方法有可能产生异常，所以必须按照异常的方式进行处理。

主方法本身也是一个方法，所以主方法上也可以使用throws进行异常的抛出，这个时候产生的异常就会交给JVM进行处理。

如果要使用throws，那就要考虑所有的异常的情况。

# throw关键字

throw是直接编写在语句之中的，表示人为进行异常的抛出。例如：10/0这样的语句执行之后所产生的数学异常是由JVM进行异常类对象实例化了，而现在如果不希望异常类对象由系统产生，希望由用户来控制异常类实例化对象的产生，就可以使用throw来完成。

范例：** 使用throw产生异常类对象
```java
public class TestDemo {
	public static void main(String[] args) {
		try {
			throw new Exception("手动抛出的错误信息");
		} catch(Exception e) {
			e.printStackTrace(); //java.lang.Exception: 手动抛出
        //at TestDemo.main(TestDemo.java:10)
        }
	}
}
```

一般throw和break、continue、return一样都需要结合if判断来使用。

throw和throws的区别：throw用于方法内部表示进行手工的异常抛出，
throws主要在方法声明上使用，明确告诉用户本方法可能产生的异常，同时该方法可能不处理此异常。

# 异常处理标准格式

异常中的核心概念：try、catch、finally、throw、throws。

**范例：** 一个除法方法

要求：

- 在进行除法计算操作之前首先要打印一句语句；
- 如果在除法计算的过程之中出现有错误，则应该将异常返回给调用处；
- 不管最终是否错误产生，都要求打印一句结束信息。

```java
class MyMath {
	//如果该方法不进行异常处理，一定要进行throws抛出
	public static int div(int x, int y) throws Exception {
		int result = 0;
		System.out.println("【开始】进行除法计算");
		try {
			result = x / y;  //此处有异常，后面不执行
		} catch(Exception e) {
			throw e;
		} finally {
			System.out.println("【结束】除法计算完毕");
		}	
		return result;
	}
}
public class TestDemo {
	public static void main(String[] args) {
		try {
			System.out.println(MyMath.div(10, 0)); //【开始】进行除法计算
		} catch(Exception e) { //【结束】除法计算完毕
			e.printStackTrace();//java.lang.ArithmeticException: / by zero
        //at MyMath.div(TestDemo.java:7)
        //at TestDemo.main(TestDemo.java:19)
		}
	}
}
```

# RuntimeException类

```java

public class TestDemo {
	public static void main(String[] args) {
		String str = "100";
		int num = Integer.parseInt(str);
		System.out.println(num * 2);
	}
}
```
观察Integer类中关于parseInt()方法的定义：`public static int parseInt(String s) throws NumberFormatException`，这个方法明确抛出一个异常，但是调用时侯却没有进行异常处理，这就RuntimeException的范畴了。`NumberFormatException`的继承结构：
```
java.lang.Object
	java.lang.Throwable
		java.lang.Exception
			java.lang.RuntimeException
				java.lang.IllegalArgumentException
					java.lang.NumberFormatException
```

使用RuntimeException定义的异常类可以不需要强制性进行异常处理。

RuntimeException和Exception的区别：

- Exception是RuntimeException的父类，使用Exception定义的异常都要必须使用异常；
- RunException可以选择性进行异常处理。

常见的RuntimeException有：`ArithmeticException`、`ClassCastException`、`NullPointerException`。

# 断言：assert

断言是从JDK1.4引入的概念，指的是当程序执行到某些语句之后，其数据的内容一定是约定的内容。

**范例：** 断言
```java

public class TestDemo {
	public static void main(String[] args) {
		int num = 10;
		//中间可能经过很多的步骤，预计num的内容应该为300
		assert num == 300 : "错误：num的内容不是三百";
		System.out.println(num);
	}
}
```
如果要让断言起作用，则必须使用一个 -ea的参数。

>java -ea TestDemo
>Exception in thread "main" java.lang.AssertionError: 错误：num的内容不是三百
>at TestDemo.main(TestDemo.java:6)

其实断言作用不大，之所以引入，是为了与C++相同。

# 自定义异常类

在Java里面实际上针对于可能出现的公共的程序问题都会提供有相应的异常信息，但是很多时候这些异常信息往往不够使用，例如在进行加法处理的时候，如果两个内容的相加结果为30，那么就抛出一个AddException的异常。但是这种Java是不会去提供的，所以就自己来定义一个这种异常。

如果哦要想定义属于自己的异常类，需要继承两种父类：Exception、RuntimeException。

**范例：** 自定义异常类
```java
class AddException extends Exception {
	public AddException(String msg) {
		super(msg);
	}
}
public class TestDemo {
	public static void main(String[] args) throws Exception {
		if((10 + 20) == 30) {
			throw new AddException("错误的相加操作");//Exception in thread "main" AddException: 错误的相加操作
        //at TestDemo.main(TestDemo.java:9)
		}
	}
}
```

# 总结

1. 异常的处理流程；
2. 异常的处理格式；
3. 异常处理的模型。