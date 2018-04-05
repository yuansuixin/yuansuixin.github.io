---
title: 设计模式---代理模式（Proxy）（动态代理）
date: 2017-11-05 15:36:49
tags:
---





## 动态代理

 Java动态代理类位于java.lang.reflect包下，一般主要 涉及到以下两个类： 

 (1)**Interface InvocationHandler接口**：该接口中仅定义了 一个方法 

 - public object invoke(Object obj,Method method, Object[] args) 

 - 在实际使用时，第一个参数obj一般是指代理类，代理实例的方法被调用 第二个参数method是被代理的方法，如上例中的request()，第三个参数args 为该方法的参数数组。 这个抽象方法在代理类中动态实现。

这个接口是由一个代理实例的调用处理器来实现的。

每一个代理实例都会有一个与之关联的调用处理器。当我们调用一个代理实例的某一个方法的时候，这个方法调用就会被编码并且被派发到与之关联的他的调用处理器的invoke方法上，被调用。

invoke方法会处理代理实例上的一个方法调用，并且将真正的调用结果返回回来。当一个调用处理器关联到这个处理器的某个代理实例，我们调用这个代理实例上的某个方法的时候，这个方法就会转移到与这个实例所关联的那个调用处理器的invoke方法，由它帮我们完成调用。

(2)**Proxy**：该类即为动态代理类，作用类似于上例中的

Proxy提供了一些静态方法用于创建动态代理类和实例，他也是由这些方法所创建的动态代理类的一个父类。

ProxySubject，其中主要包含以下内容

- protected Proxy(InvocationHandler h)：构造函数， 用于给内部的h赋值。

- static Class getProxyClass (ClassLoader loader, Class[] interfaces)：获得一个代理类，其中loader是 类装载器，interfaces是真实类所拥有的全部接口的数组 。

- static Object newProxyInstance(ClassLoader loader, Class[] interfaces, InvocationHandler h)： **返回代理类的一个实例**，返回后的代理类可以当作被代理 类使用(可使用被代理类的在Subject接口中声明过的方法 )

- 所谓Dynamic Proxy是这样一种class： 它是在**运行时生成**的class，**在生成它时你 必须提供一组interface给它，然后该class 就宣称它实现了这些 interface**。你当然可 以把该class的实例当作这些interface中的 任何一个来用(多态)。当然，这个Dynamic Proxy其实就是一个Proxy，它不会替你作 实质性的工作，在生成它的实例时你必须 提供一个handler，由它接管实际的工作

- 在使用动态代理类时，我们必须实现 InvocationHandler接口 


###### 抽象接口
```
public interface Subject {
	public void request();
}

```
###### 真实角色

```
public class RealSubject implements Subject{

	@Override
	public void request() {
		System.out.println("from real subject");
	}
}
```
###### 代理角色

```
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

/**
 * 该代理类的内部属性是Object类型，实际使用的时候通过该类的构造方法传递进来一个对象
 * 此外，该类还实现了invoke方法，该方法中的method.invoke其实就是调用被代理对象的将要
 * 执行的方法，方法参数是sub，表示该方法从属于sub，通过动态代理类，我们可以在执行真实对象的方法前后
 * 加入自己的一些额外方法。
 *
 */


public class DynamicSubject implements InvocationHandler{

	//真实对象的引用，因为是动态代理，他可以代理任意一个对象，所以就要定义为Object类型。如果是RealSubject就很受限制了。
	private Object sub;
	
	
	public  DynamicSubject(Object object) {
	   this.sub=object;
	}
	
	
	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

		System.out.println("before calling:"+method);
		
		//使用的是反射，进行的调用
		method.invoke(sub, args);
		
		System.out.println(args==null);
		
		System.out.println("after calling:"+method);
		
		//这个例子没有返回值，直接返回空就可以
		return null;
	}

	
}

```
###### 客户端

```
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Proxy;

public class Client {

	public static void main(String[] args) {
		RealSubject realSubject = new RealSubject();
		
		//多态
		//代理谁就传谁
		InvocationHandler handler = new DynamicSubject(realSubject);
		
		Class<?> classType=handler.getClass();
		
	     //下面的代码一次性生成代理
		//第一个参数为类装载器，通过Class类可以获得，我们现在要动态的生成类了，这个类是由哪个类装载器装载的呢
		//代理类返回的是一个Object类型的，需要转换类型
		//运行的时候生成一个class，然后生成class类的一个对象
		Subject subject=(Subject)Proxy.newProxyInstance(classType.getClassLoader(),
				realSubject.getClass().getInterfaces(), handler);
		
		subject.request();
		
		System.out.println(subject.getClass());
	}
}

```
运行结果：

```
before calling:public abstract void com.suixin.pattern.dynamicsubject.Subject.request()
from real subject
null
after calling:public abstract void com.suixin.pattern.dynamicsubject.Subject.request()
class com.sun.proxy.$Proxy0
```

**只要使用动态代理类，有一个动态代理类就会有一个InvocationHandler，他们总是相互关联的。**代理类并不会真正的完成方法的调用，而都是由与之关联的InvocationHandler的invoke方法真正完成目标方法的调用。

下面通过这个例子，我来给大家详细的介绍一下执行的流程和注意事项，详细步骤解读：

1. 首先进行实例化，实例化已经知道的我们需要用到的类，也就是说实例化RealSubject和DynamicSubject，这里用到了多态的知识点，这里我给大家补充一下， **多态：所谓多态，就是父类型的引用可以指向子类型的对象，或者接口类型的引用可以指向实现该接口的类的实例。关于接口与实现接口的类之间的强制类型转换方式与父类和子类之间的强制类型转换方式完全一样。**

        RealSubject realSubject = new RealSubject();

   	//多态
   	//因为这里是动态代理，代理的对象不能指定为一个，所以代理谁就传谁
   	InvocationHandler handler = new DynamicSubject(realSubject);

2. 获得调用处理器的Class对象，这里使用的是getClass（）方法。获得这个Class对象是为了生成代理的时候使用。

3.一次性生成代理实例，这里的生成实例，既不是真实对象的实例也不是代理对象DynamicSubject的实例，而是java在运行的时候生成的一个Class类，然后生成这个Class类的一个对象。

    Subject subject=(Subject)Proxy.newProxyInstance(classType.getClassLoader(),realSubject.getClass().getInterfaces(), handler);
第一个参数为类加载器，通过Class类可以获得，我们现在要动态的生成类了，这个类是由哪个类装载器装载的呢，因为我们要动态的生成一个代理角色的类，而代理角色类的方法都是由调用处理器来实现的，所以这个类加载器就是调用处理器也就是代理角色的类加载器，classType.getClassLoader()。这个类的实例宣称实现了真实角色实现的所有的接口，也就是realSubject.getClass().getInterfaces()这些接口，所以生成出来的那个对象我们可以将其转换成Subject类型，（不要装换为对应的具体的实现类类型，因为并不知道他对应了那些实现类），装换之后正常的调用方法就可以了。

**生成动态代理类，而与之关联的调用处理器就是DynamicSubject**

4.调用方法的时候，就会转而由InvocationHandler的invoke方法来执行，并且将参数也分别传递过去。就会执行invoke（）方法，执行完之后再回到Client继续执行下面的代码。这就实现了动态代理，over。

---

通过这种方式，被代理的对象 (RealSubject)可以在运行时动态改变，需 要控制的接口(Subject接口)可以在运行时 改变，控制的方式(DynamicSubject类) 也可以动态改变，从而实现了非常灵活的 动态代理关系
-  动态代理是指客户通过代理类来调用其它 对象的方法
-  动态代理使用场合:

    调试 

    远程方法调用(RMI)

![这里写图片描述](http://img.blog.csdn.net/20171113172042542?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

实现动态代理的步骤：
1. 创建一个实现接口InvocationHandler的 类，它必须实现invoke方法
2. 创建被代理的类以及接口
3. 通过Proxy的静态方法 newProxyInstance(ClassLoader loader, Class[] interfaces, InvocationHandler h) 创建一个代理 
4. 通过代理调用方法

我想大家对于代理模式已经有了一个了解了，但是我们为了更好的掌握它，还是得再巩固一下，我们练习一下带有参数传递的动态代理模式。



```
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.List;
import java.util.Vector;

public class VectorProxy implements InvocationHandler{
	private Object proxyObj;
	
	public VectorProxy(Object object) {
		this.proxyObj=object;
	}

	public static Object factory(Object object) {
		Class<?> classType=object.getClass();
		
		return Proxy.newProxyInstance(classType.getClassLoader(), 
				classType.getInterfaces(), new VectorProxy(object));
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		System.out.println("before calling:"+method);
		
		if(null!=args) {
			for(Object object:args) {
				System.out.println(object);
			}
		}
		
		Object object=method.invoke(proxyObj, args);
		
		System.out.println("after calling :"+method);
		return object;
	}
	
	public static void main(String[] args) {
		//vList是运行时动态生成的类的实例
		List vList=(List) factory(new Vector());
		
		//我们把这个动态生成的实例的名字打印出来
		System.out.println(vList.getClass().getName());
		
		vList.add("new");
		vList.add("York");
		
		System.out.println();
		System.out.println(vList);
		
		vList.remove(0);
		System.out.println(vList);
		
	}
}

```
运行结果：

```
com.sun.proxy.$Proxy0
before calling:public abstract boolean java.util.List.add(java.lang.Object)
new
after calling :public abstract boolean java.util.List.add(java.lang.Object)
before calling:public abstract boolean java.util.List.add(java.lang.Object)
York
after calling :public abstract boolean java.util.List.add(java.lang.Object)

before calling:public java.lang.String java.lang.Object.toString()
after calling :public java.lang.String java.lang.Object.toString()
[new, York]
before calling:public abstract java.lang.Object java.util.List.remove(int)
0
after calling :public abstract java.lang.Object java.util.List.remove(int)
before calling:public java.lang.String java.lang.Object.toString()
after calling :public java.lang.String java.lang.Object.toString()
[York]
```
我们一起来分析一下这个程序，其实原理都是一样的。我把所有的内容写到了一个类里面。

1. 首先从入口开始说起吧，定义了List，vList是运行时动态生成的类的实例。也就是factory方法的返回值。factory方法有一个参数object代表着传入的要代理的对象。factory返回的值是Object，但是newProxyInstance生成的类是实现了传入的参数所实现的所有的方法，new Vector（）实现了List的接口，那么动态代理也肯定实现了这个接口，可以通过强制类型转换为List类型。
2. 然后调用add方法，传入一个“new”进去，当你调用生成代理对象的任何一个方法，都会立刻转由invoke方法执行。将参数传给invoke方法，我们遍历参数的数组args就会打印出来。继续执行invoke方法，执行完之后，再回来执行add（“york"）;再重复上面的执行过程。
3. 我们打印出来vList看看是什么，其实和上面的过程是一样的，打印vList相当于调用了vList.toString();方法，过程和上面调用方法的步骤一致，只是没有传参数而已。
  4.调用remove方法也是同样的道理，我就不再重复了。

动态代理是能够用一个动态代理类，代理多个真实对象，那么我们举例来看一下，这里我就不详细的介绍了。

公共的接口：
```
public interface Foo {
	void doAction();
}

```
第一个真实的角色：
```
public class FoolImp1 implements Foo{

	@Override
	public void doAction() {
		System.out.println("in FooImpl doAction");
		
	}

}

```
第二个真实角色：
```
public class FooImpl2 implements Foo {

	@Override
	public void doAction() {
	System.out.println("in FooImp2 doAction");
		
	}	
}

```

```
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class CommonInvocationHandler implements InvocationHandler{

	private Object object;
	 public CommonInvocationHandler(Object object) {
	this.object=object;
	}
	
	 public CommonInvocationHandler() {
	}
	 
	 
	 public void setObject(Object object) {
		this.object = object;
	}
	
	//代理角色
	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		return method.invoke(object, args);
		
	}

}

```
客户端：
```
import java.lang.reflect.Proxy;
import java.util.logging.Handler;

public class Client {

	public static void main(String[] args) {
		CommonInvocationHandler cHandler=new CommonInvocationHandler();
		
		Foo foo=null;
		
		cHandler.setObject(new FoolImp1());
		
		foo=(Foo)Proxy.newProxyInstance(Foo.class.getClassLoader(), 
				new Class[] {Foo.class}, cHandler);
		
		foo.doAction();
		
		System.out.println("==================================================");
	
		cHandler.setObject(new FooImpl2());
		
		foo=(Foo)Proxy.newProxyInstance(Foo.class.getClassLoader(),
				new Class[] {Foo.class}, cHandler);
		
		foo.doAction();

	}
}
```
运行结果：

```
in FooImpl doAction
==================================================
in FooImp2 doAction
```
这个例子中FoolImp1、FoolImp2是两个真实的角色，用一个代理Proxy代理他们两个，通过在运行时期生成动态代理实例来代理，完成真实角色要实现的方法，其实，方法都是在invoke方法中得到真实调用的，代理实例不会真正的真实角色的方法，最终还是由真实角色里实现的。



[我的csdn博客](https://blog.csdn.net/prairie97)