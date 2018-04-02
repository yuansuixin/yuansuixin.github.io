---
title: 设计模式--工厂模式解析
date: 2015-10-02 22:06:04
tags:
---



在看工厂模式之前我们先了解一下面相对象的原则。

面向对象设计的基本原则

- OCP开闭原则：一个软件的实体应当对扩展开放，对修改关闭。
- DIP依赖倒转原则：要针对接口编程，不要针对实现编程
- LOD迪米特法则：只与你直接的朋友通信，而避免和陌生人通话。

# 工厂模式

实现了创建者和调用者的分离，下面我用汽车类的例子来介绍。

详细分类

- 简单工厂模式
- 工厂方法模式
- 抽象工厂模式

##### 简单工厂模式

也称之为静态工厂模式，项目开发中通常使用

(下面的例子对比了使用工厂模式和不适用工厂模式的情况)
工厂类：两种方式都可以

```
//创建一个车工厂类用来创建汽车，这个类里的方法需要是static的
public class CarFactory {

	public static Car createCar(String type) {
		if("奥迪".equals(type)) {
			return new Audi();
		}else if ("比亚迪".equals(type)) {
			return new Byd();
		}else {
			return null;	///违反了开闭原则
		}
	}
}

```

另一种方式：

```
public class CarFactory2 {

	public static Car createAudi() {
		return new Audi();
	}
	public static  Car  createByd() {
		return new Byd();
	}
}

```

接口

```
//定义一个车的接口，供各种类型的汽车实现
public interface Car {

	void run();
}

```

具体实现：

```
public class Audi implements Car {

	@Override
	public void run() {
		System.out.println("奥迪再跑");
	}

}

```

```
public class Byd implements Car{

	@Override
	public void run() {
		// TODO Auto-generated method stub
		System.out.println("byd再跑");
	}

}

```

客户端测试

```
/**
 * 不使用工厂模式的情况
 * @author yuan
 *
 */
public class Client01 {

	public static void main(String[] args) {
		Car car= new Audi();
		Car car2 = new Byd();
		car.run();
		car2.run();
	}
}

```

```
/**
 * 简单工厂情况下
 * @author yuan
 *
 */

public class Client02 {

	public static void main(String[] args) {
		Car car = CarFactory.createCar("奥迪");
		Car car2 = CarFactory.createCar("比亚迪");
			
		car.run();
		car2.run();
	}
}

```

执行结果：

```
奥迪再跑
byd再跑

```

简单工厂模式违背了面向对象编程的开闭原则，所以进一步发展就有了咱们下面要介绍的工厂方法模式，其实工厂方法模式在理论上是符合面相编程设计的原则的，但是实用性不如简单工厂模式大，他定义了太多的类和接口，每个具体的车类都需要有一个对应的车工厂，没有简单工厂模式简洁。在实际的开发中，简单工厂模式较为实用。

##### 工厂方法模式

根据设计理论上工厂方法模式占优势，实际上比简单工厂模式要复杂

```
//创建一个车工厂的接口，来供其他的具体车类实现
public interface CarFactory {

	Car createCar();
}

```

```
public interface Car {

	void run();
}

```

```
public class Audi implements Car {

	@Override
	public void run() {
		System.out.println("奥迪再跑");
		
	}

}

```

```
public class AudiFactory implements CarFactory{

	@Override
	public Car createCar() {
		return new Audi();
	}

}

```

```
public class Benz implements Car{

	@Override
	public void run() {
		// TODO Auto-generated method stub
	System.out.println("奔驰在跑");	
	}
	
}

```

```
public class BenzFactory implements CarFactory{

	@Override
	public Car createCar() {
		// TODO Auto-generated method stub
		return new Benz();
	}

}

```

```
public class Byd implements Car{

	@Override
	public void run() {
		// TODO Auto-generated method stub
		System.out.println("byd再跑");
	}

}

```

```
public class BydFactory implements CarFactory {

	@Override
	public Car createCar() {
		// TODO Auto-generated method stub
		return new Byd();
	}

}

```

```
public class Client {

	public static void main(String[] args) {
		Car car= new AudiFactory().createCar();
		car.run();
		
		Car car2 = new BydFactory().createCar();
		car2.run();
	}
}

```

执行结果：

```
奥迪再跑
byd再跑

```

工厂方法模式不修改已有类的前提下，通过增加新的工厂类实现扩展。工厂方法模式在理论上符合面向对象设计的原则，但是带来了类的冗余和拓展，所以实际中不大使用。

下面来看一下最后一种抽象工厂模式，也是最复杂的一种

##### 抽象工厂模式

用来生产不同产品族的全部产品（对于增加新的产品，无能为力，支持增加产品族）

![这里写图片描述](http://img.blog.csdn.net/20171110182731351?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```
//创建车工厂
public interface CarFactory {
	Engine createEngine();
	Seat createSeat();
	Tyre createTyre();
}

```

创建发动机接口

```
//
public interface Engine {
void run();
void start();
}
//高端发动机
class LuxuryEngine implements Engine{

	@Override
	public void run() {
		System.out.println("z转得快");
		
	}

	@Override
	public void start() {
		// TODO Auto-generated method stub
		System.out.println("启动快，可以自动启停");
	}
	
}

//高端发动机
class LowEngine implements Engine{

	@Override
	public void run() {
		System.out.println("z转得慢");
		
	}

	@Override
	public void start() {
		// TODO Auto-generated method stub
		System.out.println("启动慢");
	}
	
}

```

创建座椅接口

```
public interface Seat {

	void massage();
}

class LuxurySeat implements Seat{

	@Override
	public void massage() {
		// TODO Auto-generated method stub
		System.out.println("可以自动按摩");
	}
	
}

class LowSeat implements Seat{

	@Override
	public void massage() {
		// TODO Auto-generated method stub
		System.out.println("不可以自动按摩");
	}
	
}

```

创建轮胎接口

```
public interface Tyre {

	void revolve();
}

class LuxuryTyre implements Tyre{

	@Override
	public void revolve() {
		// TODO Auto-generated method stub
		System.out.println("旋转不磨损");
	}
	
}

class LowTyre implements Tyre{

	@Override
	public void revolve() {
		// TODO Auto-generated method stub
		System.out.println("磨损快");
	}
}

```

低端类工厂

```
public class LowCarFactory implements CarFactory{

	@Override
	public Engine createEngine() {
		// TODO Auto-generated method stub
		return new LowEngine();
	}

	@Override
	public Seat createSeat() {
		// TODO Auto-generated method stub
		return new LowSeat();
	}

	@Override
	public Tyre createTyre() {
		// TODO Auto-generated method stub
		return new LowTyre();
	}

}

```

高端类工厂

```
public class LuxuryCarFactory implements CarFactory {

	@Override
	public Engine createEngine() {
		// TODO Auto-generated method stub
		return new LuxuryEngine();
	}

	@Override
	public Seat createSeat() {
		// TODO Auto-generated method stub
		return new LuxurySeat();
	}

	@Override
	public Tyre createTyre() {
		// TODO Auto-generated method stub
		return new LuxuryTyre();
	}
}

```

说一下工厂模式的应用场景
![这里写图片描述](http://img.blog.csdn.net/20171110182745587?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

[我的csdn博客](https://blog.csdn.net/prairie97)