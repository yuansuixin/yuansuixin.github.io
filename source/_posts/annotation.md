---
title: Java中的注解（Annotation)详细解析
date: 2017-12-15 15:39:00
tags:
---



###### Annotation工作方式：

从java5.0以来，提供了一个真实的annotation功能：允许开发者定义、使用自己的额annotation类型，此功能由一个定义annotation类型的语法和一个描述annotation声明的语法，读取annotation的API，一个使用annotation修饰的Class文件，一个annotation处理工具apt组成。


annotation**并不会直接影响代码语义**，但是它能够工作的方式被看做类似程序的工具或者类库，他会反过来对正在运行的程序语义有所影响。annotation可以从源文件，class文件或者以在运行时反射的多种方式被读取。


注解可以用在方法，类，变量等上面，注解的使用范围很广。

**Override注解表示子类要重写（override）父类方法**


```
public class OverrideTest {

	@Override
	public String toString() {
		return "This is OverrideTest";
	}
	
	public static void main(String[] args) {
		OverrideTest overrideTest = new OverrideTest();
		System.out.println(overrideTest);
	}
}

```

```
This is OverrideTest
```
**Deprecate注解表示方法是不建议被使用的。**


```
import java.util.Date;

public class DeprecatedTest {
	@Deprecated
	public void doSomething() {
		System.out.println("do something ");
	}
	
	public static void main(String[] args) {
		
		DeprecatedTest deprecatedTest= new DeprecatedTest();
		//不建议被使用的
		deprecatedTest.doSomething();
		
		
//		Date d= new Date();
//		System.out.println(d.toLocaleString());
	}
}

```

**SuppressWarnings注解表示抑制警告。**


```
import java.util.Date;
import java.util.Map;
import java.util.TreeMap;

public class SuppressWarningsTest {

	@SuppressWarnings("unchecked")//不检查
	public static void main(String[] args) {
		Map map=new TreeMap();
		
		map.put("hello", new Date());
		
		System.out.println("hello");
	}
}
```

```
import java.util.Date;
import java.util.Map;
import java.util.TreeMap;

public class SuppressWarningsTest {

	@SuppressWarnings({"unchecked","deprecation"})//不检查，不推荐使用的
	public static void main(String[] args) {
		Map map=new TreeMap();
		
		map.put("hello", new Date());
		
		System.out.println("hello");
		
		Date date=new Date();
		System.out.println(date.toLocaleString());
		
	}
}

```
**注解跟着值的时候可以使用大括号，也可以省略。@SuppressWarnings({"unchecked","deprecation"})**

以上就是注解的使用方式，我们也可以自己定义注解，下面来介绍一下自定义注解。


当注解中的属性名为value时，在对其赋值时可以不指定属性的名称而直接写上属性值即可，除了value以外的其他值都需要使用name=value这种赋值方式，即明确指定给谁赋值。


```

public @interface AnnotationTest {

	//定义属性
	String value();

}

```

```

@AnnotationTest(value="hello")

public class AnnoationUsage {

	@AnnotationTest(value="world")
	public void method() {
		System.out.println("usage of annoation");
	}
	
	public static void main(String[] args) {
		AnnoationUsage annoationUsage = new AnnoationUsage();
		annoationUsage.method();
	}
	
}

```
注解的值可以默认的具体赋值，使用@interface自行定义Annotation形态时，实际上是自动继承了java.lang.annotation.Annotation接口由编译程序自动为您完成其他产生的细节，**在定义Annotation形态时，不能继承其他的Annotation形态或是接口。**

当我们使用@interface关键字定义一个注解时，该注解隐含地继承了java.lang.annotation.Annotation接口；如果我们定义了一个接口，并且让该接口继承自Annotation，那么我们所定义的接口依然还是接口而不是注解；Annotation本身是接口而不是注解。可以与Enum类比。

定义annotation类型时也可以使用包来管理类别，方式同于类的导入功能

###### 告知编译器如何处理@Retention

使用java.lang.annotation.Retention，在定义时要指定java.lang.annotation.RetentionPolicy的枚举值之一

java.lang.annotation.Retention形态可以在你定义Annotation时，指示编译程序该如何对待您的自定义的Annotation。预设上编译程序会将Annotation信息留在.class档案中，但不被虚拟机读取，而仅用于编译程序或工具程序运行时提供信息。

Retention和RetentionPolicy总是成对出现的。
```
public enum RetentionPolicy {

	SOURCE,//编译程序处理完annotation信息后就完成任务
	CLASS,//编译程序将annotation储存于Class档中，默认就是这个
	RUNTIME//编译程序将annotation储存于Class文档中，可以由VM读入
}

```
为SOURSE的例子是
@SuppressWarnings仅在编译时期告知编译程序来抑制警告，所以不必将这个信息储存于class文档
为RUNTIME的时机，可以像是你使用java设计一个程序代码分析工具，你必须让vm能读出Annotation信息，以便在分析程序时使用，**搭配反射机制就可以达到这个目的**

定义annotation时必须设定RetentionPolicy为RUNTIME，也就是可以在vm中读取Annotation信息

```
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

//自定义一个注解
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {

	String hello() default "zhangsan";
	String world();
}

```


```
@MyAnnotation(hello="beijing",world="shanghai")
public class MyTest {

//一个方法可以由多个注解修饰
	@MyAnnotation(hello="tianjin",world="shangdi")
	@Deprecated
	@SuppressWarnings("unchecked")
	public void output() {
		System.out.println("output method");
	}
}

```

```
import java.lang.reflect.Method;


public class MyReflaction {

	public static void main(String[] args)throws Exception {
		
		MyTest myTest = new MyTest();
		
		Class<MyTest> class1= MyTest.class;
		
		Method method =class1.getMethod("output", new Class[] {});
		
		//判断是否存在这样也一个Annotation
		if(method.isAnnotationPresent(MyAnnotation.class)) {
			method.invoke(myTest, new Object[] {});
			
			//得到一个Annotation，返回这个Annotation的一个实例
			MyAnnotation myAnnotation =method.getAnnotation(MyAnnotation.class);
			
			//获得这个Annotation的属性值
			String hello=myAnnotation.hello();
			String world=myAnnotation.world();
			
			System.out.println(hello+" ,"+world);
		}	
		
		//输出结果完全是由RetentionPolicy决定的，只有RUNTIME的可以读取到
		Annotation[] annotations=method.getAnnotations();
		
		//必须RetentionPolicy为RUNTIME才可以读取到
		for(Annotation annotation: annotations) {
			System.out.println(annotation.annotationType().getName());
		}
	}
}

```




运行结果：


```
output method
tianjin ,shangdi
MyAnnotation
java.lang.Deprecated
```


这个例子主要练习了几个和反射、注解有关的方法。只要理解了反射机制就很容易理解了，我再注释中都已经详细的介绍了，这里就不过多的描述了。



###### 限定annotation使用对象@Target

使用java.lang.annotation.Target可以定义其使用之时机，在定义时要指定java.lang.annotation.ElementType的枚举值之一


```

package java.lang.annotation
public enum ElementType {

	TYPE,//适用Class,interface ,enum
	FIELD,//适用文件
	METHOD,//适用于方法
	PARAMETER,//适用method上之parameter
	CONSTRUCTOR,//适用constructor
	LOCAL_VARIABLE,//适用局部变量
	ANNOTATION_TYPE,//适用annotation形态
	PACKAGE//适用package	
}

```


###### 要求为API文件@Documented
想要在使用者制作javaDoc文件的同时，也一并将Annotation的信息加入至API文档中使用。

###### 子类是否继承父类@Inherited

预设上父类中的annotation并不会被继承至子类中，可以在定义Annotation形态时加上java.lang.annotation.Inherited形态的Annotation。


注解在实际中的应用，注解主要应用在单元测试中，我来简单的说一下。

Junit有两个经典的版本，Junit3和Junit4。

Junit3继承TestCase,方法必须以test开头。keep the bar green to keep the code clean.这些底层都是通过反射机制实现的。

Junit3是基于反射的，Junit4是基于反射和注解的

JUnit4的执行的一般流程：
1.	首先获得待测试类所对应的Class对象。
2. 然后通过该Class对象获得当前类中所有public方法所对应的Method数组。
3.	遍历该Method数组，取得每一个Method对象
4.	调用每个Method对象的isAnnotationPresent(Test.class)方法，判断该方法是否被Test注解所修饰。
5.	如果该方法返回true，那么调用method.invoke()方法去执行该方法，否则不执行。



[我的csdn博客](https://blog.csdn.net/prairie97)