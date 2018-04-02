---
title: Java中的内部类详细解析
date: 2017-03-02 22:14:09
tags:
---



来说一说内部类，内部类一共分为四种，分别是

1. 静态内部类
2. 局部内部类
3. 成员内部类
4. 匿名内部类

## 静态内部类

类的成员

1. 成员变量
2. 成员方法
3. 构造方法
4. 代码块
5. 静态代码块
6. 内部类

按照内部类声明方式

1. 静态内部类
   - static
   - 属于类：类比于静态变量 静态方法，没有外部类的引用（不需要对象）（直接使用外部类的this也就是意味着不能直接访问外部类的非静态属性

###### 总结

1. **静态内部类可以直接访问外部类的静态成员变量，静态内部类不可以直接 访问外部类的非静态成员变量**
2. 调用方式：

- 导包的时候倒到外部类名为止
  外部类类名.内部类类名 内部类对象名=new 外部类类名.内部类类名（）；

- 导包的时候倒到内部类的类名为止

  内部类类名 内部类的对象名 =new 内部类类名 （）；

  - 内部类的对象名.内部类方法名（）；

```
public class StaticInnerClass {
	 static  int a=12;
	 //静态内部类
     public static class Moo{
    	 int b=13;
    	 public void add(){
    		 int c=14;
    		 System.out.println(a);
    	 }
     }
     
     public static void main(String[] args) {
		Moo m=new Moo();
		m.add();
		
	 }
}

```

```
import com.qf.oop.innerclass.StaticInnerClass;
import com.qf.oop.innerclass.StaticInnerClass.Moo;
public class Test {
	public static void main(String[] args) {
		/*StaticInnerClass.Moo m=new StaticInnerClass.Moo();
		m.add();*/
		Moo m=new Moo();
		m.add();
	}
}

```

## 局部内部类

方法中
外部方法中的内部类，访问外部类局部变量

注意：

1. 局部内部类随方法的调用而被加载
2. 也可以访问外部类的私有属性 持有外部类应用可以使用this
3. 局部内部类的对象只能在该类中创建
4. 局部内部类当中，如果访问外部类的局部变量（方法中的变量）一定是常量（使用final修饰）

```
public class LocInnerClass {
   public static void main(String[] args) {
	  final int a=12;
	  class Moo{
		  int b=13;
		  public void add(){
			  int c=14;
			 // a++;
			  System.out.println(a);
		  }
	  }
	  Moo m=new Moo();
	  m.add();
	  
	  
	  LocInnerClass l=new LocInnerClass();
	  l.test();
   }
   
   public void test(){
	   final int a=12;
		  class Moo{
			  int b=13;
			  public void add(){
				  int c=14;
				 // a++;
				  System.out.println(a);
				  System.out.println(this);
			  }
		  }
		  Moo m=new Moo();
		  m.add();
   }
}

```

## 成员内部类

```
/**
 * 2.成员内部类 可以使用4种访问权限符修饰
 *   调用方式
 *     第一种方式：
 *       外部类类名 外部类对象名=new 外部类类名（）；
 *       外部类类名.内部类类名 内部类对象名= 外部类对象名.new 内部类类名();
 *       内部类对象名.方法名（）；
 *      第二种方式：
 *        外部类类名.内部类类名 内部类对象名=new 外部类类名（）.new 内部类类名（）；
 *        内部类对象名.方法名（）；
 * 
 *
 */
public class InnerClass {
	int a=12;
   //成员内部类
   public class Moo{
	   int b=13;
	   public void add(){
		   int c=14;
		   System.out.println(a);
	   }
   }
   public static void main(String[] args) {
	   /*InnerClass in=new InnerClass();
	   System.out.println(in.a);*/
	   //第一种方式
	   InnerClass in=new InnerClass();
	   InnerClass.Moo m=in.new Moo();
	   m.add();
	   InnerClass.Moo m1=in.new Moo();
	   m1.add();
	   
	   //第二种方式
	   InnerClass.Moo m3=new InnerClass().new Moo();
	   m3.add();
   }
}

```

成员内部类变量名称一致的情况

```
public class InnerClass2 {
	int a=12;
   //成员内部类
   public class Moo{
	   int a=13;
	   public void add(){
		   int a=14;
		   System.out.println(a);
		   System.out.println("内部类成员变量a="+new Moo().a);
		   System.out.println("内部类成员变量a="+this.a);
		   System.out.println("外部类的成员变量a="+new InnerClass2().a);
		   System.out.println("外部类的成员变量a="+InnerClass2.this.a);
	   }
   }
   public static void main(String[] args) {
	   
	   //第二种方式
	   InnerClass2.Moo m3=new InnerClass2().new Moo();
	   m3.add();
   }
}

```

## 匿名内部类

语法：

类名 对象名 = new 类名（）{
实现抽象方法
}

注意：

匿名内部类没有构造方法

```
/**
 * 匿名内部类：没有名字
 * 语法：
 *   类名 对象名=new 类名(){
 *      实现抽象方法
 *   };
 * 注意：
 *   匿名内部类没有构造方法
 * 
 */
public class NoNameInnerClass {
   public static void main(String[] args) {
	    //AA a=new AA();
	    AA a=new BB();
	    a.add();
	    AA a1=new BB();
	    a1.add();
	    
	    //Animal an=new Animal();
	    Cat c=new Cat(){
			@Override
			public void catchMouse() {
				System.out.println("猫咪正在和老鼠玩耍");
				
			}	
	    };
	    c.catchMouse();
	    
	    Cat c1=new Cat(){

			@Override
			public void catchMouse() {
				System.out.println("小猫咪抓老鼠");
				
			}
	    	
	    };
	    c1.catchMouse();
	    
	    
	    Cat c3=new MImi();
	    c3.catchMouse();
	    
	    
   }
}
abstract class AA{
	public void run(){
		System.out.println("奔跑");
	}
	public abstract void add();
}
class BB extends AA{

	@Override
	public void add() {
		System.out.println(12);	
	}	
}
interface Animal{
	
}
interface Cat extends Animal{
	void catchMouse();
}
class MImi implements Cat{

	@Override
	public void catchMouse() {
		System.out.println("小猫咪抓老鼠");
		
	}
	
}

```

[我的csdn博客](https://blog.csdn.net/prairie97)