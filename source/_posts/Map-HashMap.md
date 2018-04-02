---
title: java中Map接口实现类HashMap、Map.Entry接口
date: 2015-05-20 21:59:39
tags:
---





来谈谈集合中的Map接口，它常用的实现类为HashMap。
![这里写图片描述](http://img.blog.csdn.net/20171108170056226?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# Map接口

------

- 除了类集，Java 2还在java.util中增加了映射。 映射（map）是一个存储关键字和值的关联 或者说是关键字/值对的对象。给定一个关 键字，可以得到它的值。关键字和值都是 对象。关键字必须是唯一的。但值是可以 重复的。有些映射可以接收null关键字和 null值。而有的则不行
- Map接口映射唯一关键字到值。关键字（key）是 以后用于检索值的对象。给定一个关键字和一个 值，可以存储这个值到一个Map对象中。当这个 值被存储以后，就可以使用它的关键字来检索它。 当调用的映射中没有项存在时，其中的几种方法 会引发一个NoSuchElementException异常。而当对 象与映射中的元素不兼容时，引发一个 ClassCastException异常。如果试图使用映射不允 许使用的null对象时，则引发一个 NullPointerException异常。当试图改变一个不允 许修改的映射时，则引发一个 UnsupportedOperationException异常
- 映射循环使用两个基本操作：get( )和put( )。使用 put( )方法可以将一个指定了关键字和值的值加入 映射。为了得到值，可以通过将关键字作为参数 来调用get( )方法。调用返回该值。
- 映射不是类集，但可以获得映射的类集“视图”。 为了实现这种功能，可以使用entrySet( )方法，它 返回一个包含了映射中元素的集合（Set）。为了 得到关键字的类集“视图”，可以使用keySet( ) 方法，返回一个Set集合不可以重复。为了得到值的类集“视图”，可以使用 values( )方法，返回一个Collection集合，可以重复。类集“视图”是将映射集成到类集 框架内的手段

```
import java.util.Iterator;
import java.util.Set;

public class MapTest3
{
	public static void main(String[] args)
	{
		HashMap map = new HashMap();
		
		map.put("a", "aa");
		map.put("b", "bb");
		map.put("c", "cc");
		map.put("d", "dd");
		map.put("e", "ee");
		System.out.println(map);
		
		Set set = map.keySet();
		
		for(Iterator iter = set.iterator(); iter.hasNext();)
		{
			String key = (String)iter.next();
			String value = (String)map.get(key);
			
			System.out.println(key + "=" + value);
		}
	}
}

```

```
a=aa
b=bb
c=cc
d=dd
e=ee

```

遍历Map的两种方式，一种是直接打印输出，另一种是利用Set集合中的iterator（）；方法，首先调用Map的keySet（）方法返回一个Set集合，通过Set集合中key的值可以得到value的值。

# HashMap

------

HashMap类使用散列表实现Map接口。这允 许一些基本操作如get( )和put( )的运行时间 保持恒定，即便对大型集合，也是这样的 下面的构造函数定义为：

- HashMap( )

- HashMap(Map m)

- HashMap(int capacity)

- HashMap(int capacity, float fillRatio)

  第一种形式构造一个默认的散列映射。
  第二种形式用m的元素初始化散列映射。
  第三种形式将散列映射的容量初始化为 capacity。
  第四种形式用它的参数同时初始化散列映 射的容量和填充比。容量和填充比的含义 与前面介绍的HashSet中的容量和填充比相同。
  HashMap实现Map并扩展AbstractMap。它 本身并没有增加任何新的方法
  应该注意的是散列映射并不保证它的元素 的顺序。因此，元素加入散列映射的顺序 并不一定是它们被迭代函数读出的顺序

# Map.Entry

------

Map.Entry接口使得可以操作映射的输入。 回想由Map接口说明的entrySet( )方法，调 用该方法返回一个包含映射输入的集合 （Set）。这些集合元素的每一个都是一个 Map.Entry对象

```
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

public class MapTest5
{
	public static void main(String[] args)
	{
		HashMap map = new HashMap();
		
		map.put("a", "aa");
		map.put("b", "bb");
		map.put("c", "cc");
		map.put("d", "dd");
		
		Set set = map.entrySet();
		
		for(Iterator iter = set.iterator(); iter.hasNext();)
		{
			Map.Entry entry = (Map.Entry)iter.next();
			
			String key = (String)entry.getKey();
			String value = (String)entry.getValue();
			
			System.out.println(key + " : " + value);
		}
	}
}

```

```
a : aa
b : bb
c : cc
d : dd

```

Entry对象里面封装了key和value。

Map是key和value的映射，在Map里面key和value并不是单独存放的，在底层会生成一个entry对象，entry对象里面封装了value和key，所以获得了entry对象就可以同时获得key和value。

[我的csdn博客](https://blog.csdn.net/prairie97)