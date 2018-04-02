---
title: Java中TreeSet底层解析和collections解析
date: 2016-03-27 22:08:22
tags:
---



## TreeSet

下图是集合框架中的接口
![这里写图片描述](http://img.blog.csdn.net/20171107204219357?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

今天我来谈谈SortedSet接口。

TreeSet实现了SortedSet接口，如果有自然的顺序，TreeSet具有排序的功能。

```
import java.util.TreeSet;

public class TreeSetTest
{
	public static void main(String[] args)
	{
		TreeSet set = new TreeSet();
		
		set.add("C");
		set.add("A");
		set.add("B");
		set.add("E");
		set.add("F");
		set.add("D");
		
		System.out.println(set);
	}
}

```

```
[A,B,C,D,E,F]

```

但是如果没有自然顺序的值还有正常的排序吗？我们一起来看看。。。

```
import java.util.Comparator;
import java.util.Iterator;
import java.util.TreeSet;

public class TreeSetTest2
{
	public static void main(String[] args)
	{
		TreeSet set = new TreeSet();

		Person p1 = new Person(10);
		Person p2 = new Person(20);
		Person p3 = new Person(30);
		Person p4 = new Person(40);

		set.add(p1);
		set.add(p2);
		set.add(p3);
		set.add(p4);

		for(Iterator iter = set.iterator(); iter.hasNext();)
		{
			Person p = (Person)iter.next();
			System.out.println(p.score);
		}

	}
}

class Person
{
	int score;

	public Person(int score)
	{
		this.score = score;
	}

	public String toString()
	{
		return String.valueOf(this.score);
	}
}

```

问题出现了，报错误了。

```
other.Person cannot be cast to java.lang.Comparable

```

不要慌张，去底层寻找解决办法。

![这里写图片描述](http://img.blog.csdn.net/20171107210401006?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

遇到错误通过文档去查找，大部分都是可以解决的哦，一定要有耐心哟!
TreeSet是带有排序的，我们刚才没有给程序说按照什么规则比较，所以我们要向往TreeSet放置对象，我们必须要告诉TreeSet排序的规则，制定好排序的规则，但是在哪里指定呢？和我一起来查看TreeSet的构造方法吧。

![这里写图片描述](http://img.blog.csdn.net/20171107210824004?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

改bug：

```
package com.shengsiyuan2;

import java.util.Comparator;
import java.util.Iterator;
import java.util.TreeSet;

public class TreeSetTest2
{
	public static void main(String[] args)
	{
		TreeSet set = new TreeSet(new PersonComparator());

		Person p1 = new Person(10);
		Person p2 = new Person(20);
		Person p3 = new Person(30);
		Person p4 = new Person(40);

		set.add(p1);
		set.add(p2);
		set.add(p3);
		set.add(p4);

		for(Iterator iter = set.iterator(); iter.hasNext();)
		{
			Person p = (Person)iter.next();
			System.out.println(p.score);
		}

	}
}

class Person
{
	int score;

	public Person(int score)
	{
		this.score = score;
	}

	public String toString()
	{
		return String.valueOf(this.score);
	}
}

class PersonComparator implements Comparator
{
	public int compare(Object arg0, Object arg1)
	{
		Person p1 = (Person) arg0;
		Person p2 = (Person) arg1;

		return p2.score - p1.score;
	}
}

```

```
40
30
20
10

```

## Collections

Collections的一些方法用起来还是很方便的，分享给大家，Collections里面的方法都是静态的，可以直接调用。
sort（）；排序
reverseOrder()；反序
shuffle（）;乱序
min();最小
max();最大

```
import java.util.Collections;
import java.util.Comparator;
import java.util.Iterator;
import java.util.LinkedList;

public class CollectionsTest
{
	public static void main(String[] args)
	{
		LinkedList list = new LinkedList();
		
		list.add(new Integer(-8));
		list.add(new Integer(20));
		list.add(new Integer(-20));
		list.add(new Integer(8));
		//自然顺序的反序操作
		Comparator r = Collections.reverseOrder();
		//排序
		Collections.sort(list, r);
		
		for(Iterator iter = list.iterator(); iter.hasNext();)
		{
			System.out.println(iter.next() + " ");
		}
		
		System.out.println();
		//乱序
		Collections.shuffle(list);
		
		for(Iterator iter = list.iterator(); iter.hasNext();)
		{
			System.out.println(iter.next() + " ");
		}
		
		System.out.println("minimum value: " + Collections.min(list));
		System.out.println("maximum value: " + Collections.max(list));
	}
}

20 8 -8 -20 
-20 20 -8 8 
minimum value: -20
maximum value: 20

```

[我的csdn博客](https://blog.csdn.net/prairie97)