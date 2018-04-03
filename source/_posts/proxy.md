---
title: 设计模式----代理模式（Proxy）（静态代理模式）
date: 2017-10-22 09:24:17
tags:
---


# 代理模式（Proxy）

代理模式分为静态代理和动态代理，有代理对象叫做静态代理，没有代理对象叫做动态代理

代理模式的作用是：为其他对象提供一个代理以控制对这个对象的访问。
在某些情况下，一个客户不想或者不能直接引用另一个对象，而代理对象可以在客户端和目标对象之间起到中介的作用。

代理模式一般涉及到的角色：
- 抽象角色：声明真实对象和代理对象的**共同接口**
- 代理对象：代理对象角色内部含有对真实对象的引用，从而可以操作真实对象，同时代理对象提供与真实对象相同的接口以便在任何时刻都能代替真实对象。同时代理对象可以在执行真实对象操作时，附加其他操作，相当于对真实对象进行封装
- 真实角色：代理角色所代表的真实对象，是我们最终要引用的对象


## 静态代理

代理模式比较抽象，我们利用代码例子来理解。

###### 抽象角色
```
//抽象角色
public abstract class Subject {

	
	public abstract void request();
}

```
###### 真实角色
```
public class RealSubject extends Subject {

	@Override
	public void request() {

		System.out.println("form real subject");
	}

}

```
###### 代理角色
```
public class ProxySubject extends Subject {

	private RealSubject realsubject; 
	
	
	public void proRequest() {
		System.out.println("pro request");
	}
	
	
	@Override
	public void request() {
		this.proRequest();
		
		if(realsubject==null) {
			realsubject = new RealSubject();
		}
		
		realsubject.request();
		
		this.postRequest();
		
	}
	
	public void postRequest() {
		System.out.println("post quest");
	}

}
```
###### 客户端

```
public class Client {

	public static void main(String[] args) {
		ProxySubject proxySubject = new ProxySubject();
		proxySubject.request();
	}
}

```
运行结果：

```
pro request
form real subject
post quest
```

- 由以上代码可以看出，客户实际需要调用的是 RealSubject类的request()方法，现在用ProxySubject 来代理 RealSubject类，同样达到目的，同时还封装了 其他方法(preRequest(),postRequest())，可以处理一 些其他问题。 
- 另外，**如果要按照上述的方法使用代理模式，那么真实角 色必须是事先已经存在的，并将其作为代理对象的内部属性。**但是实际使用时，一个真实角色必须对应一个代理 角色，如果大量使用会导致类的急剧膨胀；此外，如果事 先并不知道真实角色，该如何使用代理呢？这个问题可以 通过Java的动态代理类来解决

[我的csdn博客](https://blog.csdn.net/prairie97)

