---
title: Java中abstract详解
date: 2017-01-28 22:12:52
tags:
---



今天来介绍一下抽象类。我们用动物类来看，上代码：

```
public class Animal {

	String name;
	
	public Animal() {
		// TODO Auto-generated constructor stub
	}
	
	public void sleep() {
		System.out.println("闭上眼睛就睡觉");
	}
	
	public void shout() {
		System.out.println("");
	}
	
}

```

```
public class Cat extends Animal{

	@Override
	public void shout() {
		System.out.println("喵喵叫");
	}
}

```

```
public class Dog extends Animal{

	@Override
	public void shout() {
		// TODO Auto-generated method stub
		System.out.println("汪汪叫");
	}
	
}

```

```
public class Test {

	public static void main(String[] args) {
		Animal animal= new Animal();
		animal.name="动物";
		animal.shout();
		animal.sleep();
		System.out.println();
		
		Dog dog = new Dog();
		dog.name= "旺财";
		System.out.print("狗狗"+dog.name);
		dog.shout();
		dog.sleep();
		
		System.out.println();
		
		Cat cat = new Cat();
		cat.name="小花";
		System.out.print("猫咪"+cat.name);
		cat.shout();
		cat.sleep();
	}
}

```

运行结果：

```
闭上眼睛就睡觉

狗狗旺财汪汪叫
闭上眼睛就睡觉

猫咪小花喵喵叫
闭上眼睛就睡觉

```

从上面的类中我们可以发现animal类中的shout（）方法在生活中几乎是不可能用到的，于是我们为了代码的简洁，shout（）方法不需要方法体，但是会报错误This method requires a body instead of a semicolon。于是我们便引入了abstract关键字。当我们加上abstract之后，还是会报错误The abstract method shout in type Animal can only be defined by an abstract class。那么怎么办呢？抽象方法需要在抽象类中定义。

```
public abstract class Animal {

	String name;
	
	public Animal() {
		// TODO Auto-generated constructor stub
	}
	
	public void sleep() {
		System.out.println("闭上眼睛就睡觉");
	}
	
	public abstract void shout() ;
}

```

```
/**
 * 动物类
 * shout(); 这个方法里的方法体没有用 但这个方法不能注释 让子类重写
 * 1.abstract抽象的
 *   The abstract method shout in type Animal can only be defined by an abstract class
 * 2.如何定义一个抽象方法
 *   语法：
 *     【访问权限符】 abstract 返回值 方法名();
 *   eg：
 *      public abstract void shout();
 * 3.如何定义一个抽象类
 *   语法：
 *    【访问权限符】  abstract class 类名{}
 *   eg:
 *    public abstract class Animal {
 *    }
 * 4.总结
 *     A.抽象方法一定要在抽象类中
 *     B.抽象类中可以有   0 1 多个 抽象方法
 *     C.抽象类不能直接实例对象 只能创建子类对象 抽象类一定要被继承
 *       Cannot instantiate the type Animal
 *     D.子类继承父类 如果父类是抽象类 子类一定要实现父类里所有的抽象方法 除非子类也是抽象类
 *     E.abstract可以修饰   类  方法
 *               不能修饰   成员变量 构造方法
 *     F.抽象类中的成员
 *               成员变量   成员方法  构造方法       
 *        
 */

```

总结一下知识点：

1. 语法

   A.抽象类和抽象方法必须使用 abstract修饰
   B.抽象类不能直接实例 只能被继承
   C.抽象类必须有构造方法 创建子类对象时候需要
   D.抽象类可以有至少0个抽象方法
   E.抽象方法只有声明没有实现（名字 没有方法体）
   F.public abstract void shout(){} 不是抽象方法
   G.子类必须重写抽象方法，如果不重写，自己也得是抽象类

2.意义
A.抽象类为所有子类提供了一个通用模板,子类可以在此模板上进行扩展
B.通过抽象类，可以避免子类设计随意性
C.通过抽象类，可以严格限制子类的设计，使得子类更加通用

```
抽象类就是用来作父类的用来被继承的

```

问题1：
Animal an=new Animal(); 没有一种生物叫Animal 创建Animal没有意义
拒绝实例
解决方法：
Animal类 定义为抽象类
抽象类不能直接实例但可以被继承

问题2:
dog可以重写shout也可以不重写 如果希望dog必须重写shout方法 不重写就有编译错误

解决方法：
Animal类 里的shout方法定义为抽象方法
抽象方法只有声明，没有实现

[我的csdn博客](https://blog.csdn.net/prairie97)