---
title: Java三大特性
date: 2015-03-02 21:58:01
tags:
---



java最大的特点就是面向对象、可跨平台。Java的三大特性分别是继承（Inheritance）、封装（encapsulation ）、多态。（polymorphism ）。

## 继承

------

继承就是子类继承父类的属性和方法。就是生活中的儿子继承了父亲，是现实生活中的例子在java语言中的一个抽象。java是单继承的，表示一个类智能从另一个类继承，（被继承的类叫做父类（基类base class），继承的类叫做子类），java中的继承使用extends关键字。

```
public class Child extends Parent{
	public Child() {
		System.out.println("child");
	}
	public static void main(String[] args) {
		Child child = new Child();
	}
}

class Parent{
	public Parent() {
		System.out.println("parent");
	}
}
```

当生成子类对象时，Java默认首先调用父类的不带参数的构造方法，然后执行该构造方法，生成父类的对象。接下来，再去调用子类的构造方法，生成子类的对象。【要想生成子类的对象，首先需要生成父类的对象，没有父类对象就没有子类对象。比如说：没有父亲，就没有孩子】

```
public class Child extends Parent{
	public Child() {
		super(1);
		System.out.println("child");
	}
	public static void main(String[] args) {
		Child child = new Child();
	}
}

class Parent{
//	public Parent() {
//		System.out.println("parent");
//	}
	public Parent(int i) {
		System.out.println("parent");
	}
}

```

super关键字表示对父类对象的引用。

```
如果子类使用super（）显式调用父类的某个构造方法，那么在执行的时候就会寻找与super（）所对应的构造方法而不会再去寻找父类的不带参数的构造方法。与this一样，super也必须要作为构造方法的第一条执行语句，前面不能有其他可执行语句。

```

```
public class InheritenceTest
{
	public static void main(String[] args)
	{
		Apple apple = new Apple();

		System.out.println(apple.name);
	}
}

class Fruit
{
	//String name = "fruit";
}

class Apple extends Fruit
{
	String name = "apple";
}

```

关于继承的3点：


- 父类有的，子类也有
- 父类没有的，子类可以增加
- 父类有的，子类可以改变

关于继承的注意事项


- 构造方法不能被继承
  - 方法和属性可以被继承
  - 子类的构造方法隐式地调用父类的不带参数的构造方法
  - 当父类没有不带参数的构造方法时，子类需要使用super来显式地调用父类的构造方法，super指的是对父类的引用
  - super关键字必须是构造方法中的第一行语句。

```
public class InheritenceTest2
{
	public static void main(String[] args)
	{
		Dog dog = new Dog();
		dog.run();
	}	
}

class Animal
{
	public void run()
	{
		System.out.println("animal is running");
	}
}

class Dog extends Animal
{
	public void run()
	{	
		System.out.println("dog is running");
		super.run(); //调用父类的run方法
	}
}

```

方法重写（override）：又叫做覆写，子类与父类的方法返回类型一样、方法名称一样、参数一样，这样我们说子类与父类的方法构成了重写方法。

当两个方法形成重写关系时，可以在子类方法中通过super.run()形式调用父类的run()方法，其中super.run()不必放在第一行语句，因此此时父类对象已经构造完毕，先调用父类的run()方法还是先调用子类的run()方法是根据程序的逻辑决定的。

在定义一个类的时候，如果没有显式指定该类的父类，那么该类就会继承于java.lang.Object类（JDK提供的一个类，Object类是Java中所有类的直接或间接父类）。

## 多态

------

多态（Polymorphism）：我们说子类就是父类（菊花是花，女人是人），因此多态的意思是：父类型的引用可以指向子类的对象。

```
public class PolyTest
{
	public static void main(String[] args)
	{
		Flower rose = new Rose(); //多态
		rose.sing();
	}
}

class Flower
{
	public void sing()
	{
		System.out.println("flower is singing");
	}
}

class Rose extends Flower
{
}

```

**注意：**方法重载不是面向对象的特征，如果不是晚绑定，就不是多态，而方法重载不是晚绑定而是早绑定。（参考Thinking in java）

```
public class PolyTest
{
	public static void main(String[] args)
	{
		//Parent parent = new Parent();
		//parent.sing();
		
		//Child child = new Child();
		//child.sing();

		Parent p = new Child();
		p.sing();
	}
}

class Parent
{
	/*
	public void sing()
	{
		System.out.println("parent is singing");
	}
	*/
}

class Child extends Parent
{
	public void sing()
	{
		System.out.println("child is singing");
	}
}

```

Parent p = new Child();当使用多态方式调用方法时，首先检查父类中是否有sing()方法，如果没有则编译错误；如果有，再去调用子类的sing()方法。

```
public class PolyTest2
{
	public static  void main(String[] args)
	{
		/*
		Animal animal = new Cat();
		Animal animal2 = new Animal();

		animal2 = animal;
		animal2.sing();
		*/
		
		/*
		Animal animal = new Cat();
		Animal animal2 = new Animal();

		animal = animal2;
		animal.sing();
		*/

		/*
		Cat cat = new Cat();
		Animal animal = cat;
		animal.sing();
		*/
		
		/*
		Animal animal = new Animal();
		Cat cat = (Cat)animal;
		*/

		//向上类型转换
		Cat cat = new Cat();

		Animal animal = cat;

		animal.sing();

		//向下类型转换
		Animal a = new Cat();

		Cat c = (Cat)a;

		c.sing();

	}
}

class Animal
{
	public void sing()
	{
		System.out.println("animal is singing");
	}
}

class Dog extends Animal
{
	public void sing()
	{
		System.out.println("dog is singing");
	}
}

class Cat extends Animal
{
	public void sing()
	{
		System.out.println("cat is singing");
	}
}

```

一共有两种类型的强制类型转换：

1. 向上类型转换（upcast）：比如说将Cat类型转换为Animal类型，即将子类型转换为父类型。对于向上类型转换，不需要显式指定。
   1. 向下类型转换（downcast）：比如将Animal类型转换为Cat类型。即将父类型转换为子类型。对于向下类型转换，必须要显式指定（必须要使用强制类型转换）。

```
public class PolyTest3
{
	public static void main(String[] args)
	{
		//Fruit f = new Pear();
		//f.run();
		
		//Pear p = (Pear)f;
		//p.run();
		//是不可以的，父类中没有grow（），强制类型转换后可以
		//Fruit f = new Pear();
		//f.grow();

		Fruit f = new Pear();

		Pear p = (Pear)f;
		
		p.grow();
	}
}

class Fruit
{
	public void run()
	{
		System.out.println("fruit is running");
	}
}

class Pear extends Fruit
{
	public void run()
	{
		System.out.println("pear is running");
	}

	public void grow()
	{
		System.out.println("pear is growing");
	}
}

```

当你想使用子类特有的方法，而此方法没有在父类中出现的时候可以使用强制类型转换（向下类型转换）。

```
public class PolyTest4
{
	public static void main(String[] args)
	{
		A a = null;

		if(args[0].equals("1"))
		{
			a = new B();	
		}
		else if(args[0].equals("2"))
		{
			a = new C();
		}
		else if(args[0].equals("3"))
		{
			a = new D();
		}

		a.method();
	}
}

class A
{
	public void method()
	{
		System.out.println("A");
	}
}

class B extends A
{
	public void method()
	{
		System.out.println("B");
	}
}

class C extends A
{
	public void method()
	{
		System.out.println("C");
	}
}

class D extends A
{
	public void method()
	{
		System.out.println("D");
	}
}

```

晚绑定，编译的时候不知道，等到执行的时候才能确定下来具体的子类。

Connecting a function call to a function body is called binding.（将函数体和函数调用关联起来，就叫绑定）

早绑定（Early binding）

```
When binding is performed before the program is run (by the compiler and linker), it' s called early binding
在程序运行之前（也就是编译和链接时），执行的绑定是早绑定。

```

晚绑定（late binding）

```
late binding, which means the binding occurs at runtime, based on the type of the object. When a language implements late binding, there must be some mechanism to determine the  type of the object at runtime and call the appropriate member function.

```

早绑定的优点是:

- 编译效率
- 代码提示(代码智能感知)
- 编译时类型检查

晚绑定的优点是:

- 不用申明类型
- 对象类型可以随时更改

```
public class PolyTest5
{
	/*
	public void run(BMW bmw)
	{
		bmw.run();
	}

	public void run(QQ qq)
	{
		qq.run();
	}
	*/

	public void run(Car car)
	{
		car.run();
	}

	public static void main(String[] args)
	{
		/*
		 PolyTest5 test = new PolyTest5();

		BMW bmw = new BMW();

		test.run(bmw);

		QQ qq = new QQ();

		test.run(qq);
		*/
		
		PolyTest5 test = new PolyTest5();

		Car car = new BMW();

		test.run(car);
		//向上类型转换
		QQ qq = new QQ();

		test.run(qq);
	}
}

class Car
{
	public void run()
	{
		System.out.println("car is running");
	}
}

class BMW extends Car
{
	public void run()
	{
		System.out.println("BMW is running");
	}
}

class QQ extends Car
{
	public void run()
	{
		System.out.println("QQ is running");
	}
}

```

多态屏蔽掉了子类给我们带来的差异性


## 封装

------

封装（英语：Encapsulation）是指一种将抽象性函式接口的实现细节部份包装、隐藏起来的方法。
封装可以被认为是一个保护屏障，防止该类的代码和数据被外部类定义的代码随机访问，其实前面用到了封装。

访问权限符：
![这里写图片描述](http://img.blog.csdn.net/20171108110832150?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

封装的优点：

1. 良好的封装能够减少耦合。
2. 类内部的结构可以自由修改。
3. 可以对成员变量进行更精确的控制。
4. 隐藏信息，实现细节。

修改属性的可见性：

```
public class Person {
    private String name;
    private int age;
}

```

对每个值属性提供对外的公共方法访问：

```
public class EncapTest{
 
   private String name;
   private String idNum;
   private int age;
 
   public int getAge(){
      return age;
   }
 
   public String getName(){
      return name;
   }
 
   public String getIdNum(){
      return idNum;
   }
 
   public void setAge( int newAge){
      age = newAge;
   }
 
   public void setName(String newName){
      name = newName;
   }
 
   public void setIdNum( String newId){
      idNum = newId;
   }
}

```

利用封装解决非法赋值问题

```
package other;
import other.Other;

class Test2 {

	public static void main(String[] args) {
		
		Student student = new Student();
		student.setAge(200);
		student.setId(1001);
		student.setName("张三");
		
		student.show();
	}
	
}

class Student{
	private int id;
	private int age;
	private String name;
	
	public Student() {
		// TODO Auto-generated constructor stub
	}
	
	
	
	public Student(int id, int age, String name) {
		super();
		this.id = id;
		this.age = age;
		this.name = name;
	}


	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		if(age<0||age>150) {
			this.age=0;
		}else {
			this.age = age;
		}
		
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	
	public void show() {
		System.out.println("Student:id="+id+",name="+name+",age="+age);
	}
}

```

```
Student:id=1001,name=张三,age=0

```

近期系统的学习了java，做了一些知识的总结和思考，以博客的形式展示了出来，希望大家指点。我的第一篇博客完成，希望自己今后能够坚持下来，认真的反思与思考，总结博客，对各方面的知识能够更深入的研究学习。

[我的csdn博客](https://blog.csdn.net/prairie97)



