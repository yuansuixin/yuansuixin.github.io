---
title: Java中的反射机制深入剖析（一）
date: 2017-05-24 22:15:12
tags:
---

我们来谈谈反射，这个知识点有些难度，不好理解，我介绍的详细一些，尽量细致，不对的地方往大家指正。

我们平时编的代码和接触到的都是在java编译环境中的，而在java运行时环境中，对于任意一个类，能否知道这个类有哪些属性和方法？对于任意一个对象，能否调用它的任意一个方法？答案是**肯定的** 这种动态获取类的信息以及动态调用对象的方法的功能来自于java语言的反射机制。

java反射机制的功能

- 在运行时判断任意一个对象所属的类
- 在运行时构造任意一个类的对象
- 在运行时判断任意一个类所具有的成员变量和方法
- 在运行时调用任意一个对象的方法

编程语言分为动态语言和静态语言

动态语言：程序运行时，允许改变程序结构或变量类型。Perl,Python,Ruby是动态语言，而C++，java，C#不是动态语言。

而反射机制是java被视为动态语言的一个关键性质。这个机制允许程序在运行时透过Reflaction APIs取得任何一个已知名称的class的内部信息。包括其 modifiers（诸如public, static 等等）、superclass（ 例如Object）、实现之interfaces（例如Serializable） ，也包括fields和methods的所有信息，并可于运行时改 变fields内容或调用methods

尽管在这样的定义与分类下Java不是动态语言， 它却有着一个非常突出的动态相关机制： Reflection。这个字的意思是“反射、映象、倒 影”，用在Java身上指的是我们可以于运行时加 载、探知、使用编译期间完全未知的classes。换 句话说，Java程序可以加载一个运行时才得知名 称的class，获悉其完整构造（但不包括 methods定义），并生成其对象实体、或对其 fields设值、或唤起其methods。这种“看透 class”的能力（the ability of the program to examine itself）被称为introspection（内省、 内观、反省）。Reflection和introspection是常 被并提的两个术语。

在JDK中，主要由以下类来实现Java反射机制，这些类都 位于java.lang.reflect包中

- Class类：代表一个类。
- Field 类：代表类的成员变量（成员变量也称为类的属性）。
- Method类：代表类的方法。
- Constructor 类：代表类的构造方法。
- Array类：提供了动态创建数组，以及访问数组的元素的静态方法

了解了这么多理论，大家也晕了吧，让我们来看看反射在程序中怎么使用的。。。

来介绍一下基本的作用
**java中，无论生成某各类的多少个对象，这些对象都会对应于同一个Class对象。这个Class对象是在没有生成任何类之前由jvm帮我们生成好的，在类被装载的时候Class对象就已经生成好了。这个Class对象生成好之后就会获悉我们当前类所有的成员变量以及所有的方法。**

首先要获得class类，这里不止一种方式，我会陆续介绍给大家。

```
import java.lang.reflect.Method;

public class DumpMehtod {

//这里我们主要是学习反射，对于异常的问题就直接抛出了
	public static void main(String[] args) throws Exception {
	//获得class类的第一种方式
		Class<?> class1 = Class.forName("java.lang.Object"); 
		
		Method[] methods = class1.getDeclaredMethods();
		//增强的for循环
		for(Method method: methods) {
			System.out.println(method);
		}
	}
}

```

运行结果将获得Object类的所有方法，如下：

```
protected void java.lang.Object.finalize() throws java.lang.Throwable
public final void java.lang.Object.wait() throws java.lang.InterruptedException
public final void java.lang.Object.wait(long,int) throws java.lang.InterruptedException
public final native void java.lang.Object.wait(long) throws java.lang.InterruptedException
public boolean java.lang.Object.equals(java.lang.Object)
public java.lang.String java.lang.Object.toString()
public native int java.lang.Object.hashCode()
public final native java.lang.Class java.lang.Object.getClass()
protected native java.lang.Object java.lang.Object.clone() throws java.lang.CloneNotSupportedException
public final native void java.lang.Object.notify()
public final native void java.lang.Object.notifyAll()
private static native void java.lang.Object.registerNatives()

```

接下来再深入了解通过反射调用自己定义的方法

```
import java.lang.reflect.Method;

public class InvokeTest {
	
	public int  add(int a, int b) {
		return a+b;
	}
	
	public String echo(String string) {
		return "hello"+string;
	}

	public static void main(String[] args) throws Exception{
	    
	    //平时的调用步骤
	    // InvokeTester test = new InvokeTester();
		// System.out.println(test.add(1, 2));
		// System.out.println(test.echo("tom"));
	
	
	//获得class类的第二种方法，泛型中的？表示继承Object
		Class<?> classType = InvokeTest.class; 
		//将类进行实例
		Object incokeTest=classType.newInstance();
		//获得类的方法，
		//使用class类调用getMethod（）方法
		//第一个参数是类的名称，第二个参数为方法的形式参数所对应的class对象构成的数组
		Method addMethod = classType.getMethod("add", new Class[] {int.class,int.class});
		//调用方法的invoke（）方法，执行此方法，
		//第一个参数为此类的实例，第二个参数是为这个方法传递的参数组成的数组
		//自动装箱
		Object result=addMethod.invoke(incokeTest, new Object[]{1,2});
		//返回的肯定是一个Integer类型，这里是自动拆箱
		System.out.println((Integer)result);
		
		System.out.println("--------------------------------------------------");
		
		Method echoMethod=classType.getMethod("echo", new Class[] {String.class});
		
		Object result2=echoMethod.invoke(incokeTest, new Object[] {"  world"});
		
		System.out.println((String)result2);
	}	
}

```

运行结果：

```
3
--------------------------------------------------
hello  world

```

[我的csdn博客](https://blog.csdn.net/prairie97)