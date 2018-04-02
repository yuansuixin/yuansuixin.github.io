---
title: 设计模式--策略模式（strategy-pattern）解析
date: 2016-11-08 18:11:13
tags:
---

策略模式（Strategy Pattern）中体现了两个非常基本的面相对象设计的原则：

1. 封装变化的概念
2. 编程中使用接口，而不是对接口的实现

策略模式的定义 ：

- 定义一组算法，将每个算法都封装起来，并且 使它们之间可以互换。
- 策略模式使这些算法在客户端调用它们的时候 能够互不影响地变化

策略模式的意义

- 策略模式使开发人员能够开发出由许多可替换 的部分组成的软件，并且各个部分之间是弱连 接的关系。

- 弱连接的特性使软件具有更强的可扩展性，易 于维护；更重要的是，它大大提高了软件的可 重用性

  策略模式的组成

- 抽象策略角色：策略类，通常由一个接口或者 抽象类实现

- 具体策略角色：包装了相关的算法和行为

- 环境角色：持有一个策略类的引用，最终给客 户端调用的。

  策略模式的实现

- 策略模式的用意是针对一组算法，将每一个算 法封装到具有共同接口的独立的类中，从而使 得它们可以相互替换。

- 策略模式使得算法可以在不影响到客户端的情 况下发生变化。使用策略模式可以把行为和环 境分割开来。

- 环境类负责维持和查询行为类，各种算法则在 具体策略中提供。由于算法和环境独立开来， 算法的修改都不会影响环境和客户端

策略模式的编写步骤 ：

1. 对策略对象定义一个公共接口。

2．编写策略类，该类实现了上面的公共接口

3．在使用策略对象的类中保存一个对策略对 象的引用。

4．在使用策略对象的类中，实现对策略对象 的set和get方法（注入）或者使用构造方法完 成赋值

小提示：在eclipse里面Ctrl+t 进入到实现类的代码中去。

下面我们以计算器为例实现我们的策略模式：

###### 抽象策略角色

```
public interface Strategy {

	public int calculate(int a,int b);
	
}

```

###### 具体策略角色

```
public class AddStrategy implements Strategy{

	@Override
	public int calculate(int a, int b) {
		// TODO Auto-generated method stub
		return a+b;
	}
}

```

```
public class SubtractStrategy implements Strategy{

	@Override
	public int calculate(int a, int b) {
		// TODO Auto-generated method stub
		return a-b;
	}

}

```

```
public class MultiplyStrategy implements Strategy{

	@Override
	public int calculate(int a, int b) {
		// TODO Auto-generated method stub
		return a*b;
	}

	
}

```

```
public class DivideStrategy implements Strategy {

	@Override
	public int calculate(int a, int b) {
		// TODO Auto-generated method stub
		return a/b;
	}

}

```

###### 环境角色

```
public class Environment {

	private Strategy strategy;
	
	public Environment (Strategy strategy) {
		this.strategy= strategy;
	}
	
	public void setStrategy(Strategy strategy) {
		this.strategy = strategy;
	}
	
	public Strategy getStrategy() {
		return strategy;
	}
	
	public int calculate(int a,int b) {
		return strategy.calculate(a, b);
	}
	
}

```

测试：

```
public class Client {

	public static void main(String[] args) {
		AddStrategy addStrategy = new AddStrategy();
		Environment environment = new Environment(addStrategy);
		
		System.out.println("3+4="+environment.calculate(3,4));
		
		SubtractStrategy strategy = new SubtractStrategy();
		environment.setStrategy(strategy);
		System.out.println("3-4="+environment.calculate(3, 4));
		
		MultiplyStrategy multiplyStrategy = new MultiplyStrategy();
		environment.setStrategy(multiplyStrategy);
		System.out.println("3*4="+environment.calculate(3,4));
		
		DivideStrategy divideStrategy = new DivideStrategy();
		environment.setStrategy(divideStrategy);
		System.out.println("3/4="+environment.calculate(3,4));
	}
}

```

运行结果：

```
3+4=7
3-4=-1
3*4=12
3/4=0

```

策略模式的缺点

- 客户端必须知道所有的策略类，并自行决定 使用哪一个策略类。
- 造成很多的策略类。

解决方案

- 采用工厂方法

[我的csdn博客](https://blog.csdn.net/prairie97)