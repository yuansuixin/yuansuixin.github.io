---
title: Java中的反射机制深入剖析（二）
date: 2017-06-02 22:18:11
tags:
---



继续来谈谈反射机制

1. 要想使用反射，首先需要获得待处理类或对象所对应的Class对象。

**2. 获取某个类或某个对象所对应的Class对象的常用的3种方式：**

- **使用Class类的静态方法forName，Class.forName(“java.lang.String”);**
- **使用类的.class语法：String.class;**
- **使用对象的getClass()方法： String s=”aa”;Class<?> class=s.getClass();**

```
import java.lang.reflect.Constructor;

public class ReflactTester {

	//该方法实现对Customer对象的拷贝操作
	public Object copy(Object object) throws Exception {
		Class<?> classType=object.getClass();
		
//		System.out.println(classType.getName());
		
		Constructor constructor = classType.getConstructor(new Class[] {});
		
		Object object2= constructor.newInstance(new Object[] {});
		
		
		
		//以上两行代码等价于下面一行
//		Object object2=classType.newInstance();
		
//		
//		Constructor constructor3 = classType.getConstructor(new Class[] {String.class,int.class});
//		
//		Object object3= constructor3.newInstance(new Object[] {"hello",4});
		
		
		System.out.println(object2);
	//	System.out.println(object3);
		
		return null;		
	}
	
	
	public static void main(String[] args) throws Exception {
		ReflactTester tester= new ReflactTester();
		tester.copy(new Customer());
	}
}
class Customer{
	
	private Long id;
	
	private String name;
	
	private int age;
	
	public Customer() {
		
	}
	
	public Customer (String name, int age) {
		super();
		this.name = name;
		this.age = age;
	}

	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

}

```

运行结果：

```
Customer@15db9742

```

3.若想通过类的不带参数的构造方法来生成对象，我们有两种方式：

- 先获得 Class 对象，然后通过该 Class 对象的 newInstance()方法直接生成即可：

  ```
  Class<?> classType = String.class; 
  Object obj = classType.newInstance();

  ```

- 先获得 Class 对象，然后通过该对象获得对应的 Constructor 对象，再通过该 Constructor 对象的 newInstance()方法生成：

  ```
  Class<?> classType = Customer.class; 
  Constructor cons = classType.getConstructor(new Class[]{});
  Object obj = cons.newInstance(new Object[]{});

  ```

4.若想通过类的带参数的构造方法生成对象，只能使用下面这一种方式：

```
Class<?> classType = Customer.class; 
Constructor cons = classType.getConstructor(new Class[]{String.class, int.class}); 
Object obj = cons.newInstance(new Object[]{“hello”, 3});

```

------

我们再来看一个例子,ReflectTester类有一个 copy(Object object)方法，这个方法能够创建 一个和参数object 同样类型的对象，然后把 object对象中的所有属性拷贝到新建的对象中， 并将它返回

```
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class ReflactTester2 {

	public Object copy(Object object )throws Exception {
    	//获得对象的类型
		Class<?> classType = object.getClass();
		//System.out.println("Class:"+classType.getName());
		
		//通过无参数的构造方法构造出来类并且实例化
		Object objectCopy=classType.getConstructor(new Class[] {}).newInstance(new Object[] {});
		
		//获得对象的所有成员变量
		
		Field[] fields= classType.getDeclaredFields();
		
		for(Field field:fields) {
			//获得属性的名称
			String name=field.getName();
			
			//变量名称首字母大写
			String firstLetter=name.substring(0,1).toUpperCase();
			
			//获取到get、set方法的名称
			String getMethodName="get"+firstLetter+name.substring(1);
			String setMethodName="set"+firstLetter+name.substring(1);
			
			//获取到get、set方法
			Method getMethod=classType.getMethod(getMethodName, new Class[] {});
			Method setMethod=classType.getMethod(setMethodName, new Class[] {field.getType()});
			
			//set方法没有参数
			Object value=getMethod.invoke(object, new Object[] {});
			
			//将get到的属性值传入
			setMethod.invoke(objectCopy, new Object[] {value});
		}	
			return objectCopy;	
	}
	
	public static void main(String[] args) throws Exception{
		Customer customer = new Customer("Tom",20);
		//注意是long类型的哟
		customer.setId(1L);
		
		ReflactTester tester = new ReflactTester();
		
		Customer customer2 = (Customer) tester.copy(customer);
		
		//System.out.println(customer2.getId()+","+customer2.getName()+","+customer2.getAge());
	}
	
	
}

```

运行结果显示：

```
1,Tom,20

```

Class类是Reflection API 中的核心类，它有以下方法 :

- getName()：获得类的完整名字。

- getFields()：获得类的public类型的属性。

- getDeclaredFields()：获得类的所有属性。

- getMethods()：获得类的**public类型**的方法。

- getDeclaredMethods()：获得类的所有方法。

  Method类的invoke(Object obj,Object args[])方法**接 收的参数必须为对象**，如果参数为基本类型数据，必须转 换为相应的包装类型的对象。**invoke()方法的返回值总是 对象**，如果实际被调用的方法的返回类型是基本类型数据 ，那么invoke()方法会把它转换为相应的包装类型的对象 ，再将其返回.

下面我们来看看数组，java.lang.Array 类提供了动态创建和访 问数组元素的各种静态方法。

```
import java.lang.reflect.Array;

public class ArrayTester1
{
	public static void main(String[] args) throws Exception
	{
		Class<?> classType = Class.forName("java.lang.String");
		
		Object array = Array.newInstance(classType, 10);
		
		Array.set(array, 5, "hello");
		
		String str = (String)Array.get(array, 5);
		
		System.out.println(str);
	}
}

```

运行结果：
hello

------

创建 了一个 5 x 10 x 15 的整型数组，并把索 引位置为[3][5][10] 的元素的值为设37

```
import java.lang.reflect.Array;

public class ArrayTester2
{
	public static void main(String[] args)
	{
		int[] dims = new int[] { 5, 10, 15 };

		Object array = Array.newInstance(Integer.TYPE, dims);
		
		System.out.println(array instanceof int[][][]);

//二维数组
		Object arrayObj = Array.get(array, 3);

//Class<?> classType=arrayObj.getClass().getComponentType();
//System.out.println(classType);

//一维数组
		arrayObj = Array.get(arrayObj, 5);


		Array.setInt(arrayObj, 10, 37);

		int[][][] arrayCast = (int[][][]) array;

		System.out.println(arrayCast[3][5][10]);

		// System.out.println(Integer.TYPE);
		// System.out.println(Integer.class);

	}
}

```

```
true
37

```

1. Integer.TYPE 返回的是 int，而 Integer.class 返回的是 Integer 类所对应的 Class 对象。

反射破坏了类的封装性，可以调用私有的方法，变量，接下来我们一起来看看

```
public class PivateTest {

	private String sayHello(String name) {
		return "hello"+name;
	}
}

```

```
import java.lang.reflect.Method;

public class Private {

	public static void main(String[] args) throws Exception {
		
		PivateTest private1 = new PivateTest();
		
		Class<?> classType=private1.getClass();
		
		
		
	    Method  method = classType.getDeclaredMethod("sayHello", new Class[] {String.class});
		
		method.setAccessible(true);//压制java的访问权限的内部检查
	
		String string = (String)method.invoke(private1, new Object[] {"  zhangsan"});
		
		System.out.println(string);
	}
}

```

运行结果：

```
hello  zhangsan

```

用反射更改私有的成员变量的值，怎么实现呢，我们来看看

```
import java.lang.reflect.Field;

public class Private2 {

	
	private String name="zhangsan";
	
	public String getName() {
		return name;
	}
	
	public static void main(String[] args) throws Exception{
		
		Private2 private2 = new Private2();
		
		Class<?> classType=Private.class;
		
		Field field=classType.getDeclaredField("name");
		
		//压制java对访问修饰符的检查
		field.setAccessible(true);
		
		//对于属性的操作，直接使用get或是set方法就可以了
		field.set(private2, "lisi");
	
		System.out.println(private2.getName());
		
	}
	
}

```

运行后的结果：

```
lisi

```

众所周知Java有个Object class，是所有 Java classes的继承根源，其内声明了数 个应该在所有Java class中被改写的 methods：hashCode()、equals()、 clone()、toString()、getClass()等。其 中getClass()返回一个Class object。

- Class class十分特殊。它和一般classes一样继承自 Object，其实体用以表达Java程序运行时的classes和 interfaces，也用来表达enum、array、primitive Java types
- （boolean, byte, char, short, int, long, float, double）以及关键词void。当一个class被加载，或当加 载器（class loader）的defineClass()被JVM调用， JVM 便自动产生一个Class object。如果您想借由“修 改Java标准库源码”来观察Class object的实际生成时 机（例如在Class的constructor内添加一个println()） ，不能够！因为Class并没有public constructor
  只有java虚拟机可以创建一个Class


- Class是Reflection起源。针对任何您想探 勘的class，唯有先为它产生一个Class object，接下来才能经由后者唤起为数十 多个的Reflection APIs

**为什么获得Method object时不需指定回返类型？**

- 因为method overloading机制要求 signature必须唯一，而回返类型并非 signature的一个成份。换句话说，只要指定了method名称和参数列，就一定指出了 一个独一无二的method。（在方法重载的那个地方也是这样）

```
public class ClassTest {

	public static void main(String[] args)
	{
		Class<?> classType = Child.class;
		
		System.out.println(classType);
		
		classType = classType.getSuperclass();
		
		System.out.println(classType);
		
		classType = classType.getSuperclass();
		
		System.out.println(classType);
		
		classType = classType.getSuperclass();
		
		System.out.println(classType);
	}
	
}
class Parent{
	
}
class Child extends Parent {
	
}
```

运行结果：

```
class Child
class Parent
class java.lang.Object
null

```

什么时候能用到反射呢？

实际开发中一般是用不到反射的，除非自己去写框架肯定会用到反射，掌握了反射机制，对于以后学框架有利，理解更深入。

[我的csdn博客](https://blog.csdn.net/prairie97)