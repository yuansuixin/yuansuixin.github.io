---
title: 设计模式--单例模式
date: 2016-01-23 22:07:14
tags:
---



单例模式分为两种：饿汉式、懒汉式

既然是单例模式，那么就只有一个实例，那么构造方法就是私有的，就需要创建一个对象，而且也需要是私有的，但是需要调用所以要设置成静态的，然后提供一个方法拿到这个私有的对象。
一个类只会生成唯一的一个对象。

单例模式：饿汉式

1.私有构造方法
2.创建私有的静态对象
3.创建一个公共公开的方法，返回该私有对象

```
public class Single1 {
  private static Single1 s1=new Single1();
  private Single1(){
	  
  }
  public static  Single1 getInstance(){
	  return s1;
  }
}

```

```
public class TestSingle1 {
	public static void main(String[] args) {
		
		/*Single1 s1=new Single1();
		System.out.println(s1);
		Single1 s2=new Single1();
		System.out.println(s2);
		System.out.println(s1==s2);*/
		Single1 s1=Single1.getInstance();
		System.out.println(s1);
		Single1 s2=Single1.getInstance();
		System.out.println(s2);
		System.out.println(s1==s2);
		
	}
}

```

```
com.qf.oop.innerclass.Single1@15db9742
com.qf.oop.innerclass.Single1@15db9742
true

```

饿汉式就是更急切的new出来了对象，而懒汉式就不这样了，其实，本质是一样的，让我们来看看懒汉吧。

单例模式：懒汉式

1.私有构造方法

1. 创建私有静态对象
   3.创建公开公共的静态方法返回该私有对象

```
public class Single2 {
  private static Single2 s2=null;
  private Single2(){
	  
  }
  public static Single2 getInstance(){
	  if(s2==null){
		  s2=new Single2();
	  }
	  return s2;
  }
  /*public static Single2 getInstance(){
	  if(s2==null){
		  return new Single2();
	  }
	  return s2;  错误的
  }*/
}

```

```
public class TestSingle2 {
	public static void main(String[] args) {
		  Single2 s1=Single2.getInstance();
		  System.out.println(s1);
		  Single2 s2=Single2.getInstance();
		  System.out.println(s2);
		  System.out.println(s1==s2);
		  //Object
		  
	}
}

```

懒汉式的单例模式有一种错误的方法，我已经在代码中写出来了，大家一定要注意，仔细一点哦。

[我的csdn博客](https://blog.csdn.net/prairie97)