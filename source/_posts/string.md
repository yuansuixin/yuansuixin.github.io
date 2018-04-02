---
title: Java中String类详细解析
date: 2016-04-24 20:09:40
tags:
---

1.相等性的比较（==）
(1)对于原生数据类型来说，比较的是左右两边的值是否相等
（2）对于引用类型来说，比较左右两边的引用是否指向同一个对象，或者说左右两边的引用地址是否相同。

2.Object 类的tostring方法返回的是一个哈希code值，而string类重写了tostring方法，默认调用tostring方法。
API （Application Programming Interface），应用编程接口。
当打印引用时，实际上会打印出引用所指对象的toString()方法的返回值，因为每个类都直接或间接地继承自Object，而Object类中定义了toString()，因此每个类都有toString()这个方法。

3.equals方法
对于object类的equals方法来说，是用来判断两个对象是不是同一个对象。
对于继承了object类的其他类来说，如果重写了equals方法，才是判断内容是否一致，，如果没有重写equals方法，是判断地址是否一致。
对于String类的equals()方法来说，它是判断当前字符串与传进来的字符串的内容是否一致。

4.字符串是一个常量，创建之后值是不能被改变的。

5.String是常量，其对象一旦创建完毕就无法改变。当使用+拼接字符串时，会生成新的String对象，而不是向原有的String对象追加内容。

6、 String Pool（字符串池）

7、 String s = “aaa”;（采用字面值方式赋值）
1) 查找String Pool中是否存在“aaa”这个对象，如果不存在，则在String Pool中创建一个“aaa”对象，然后将String Pool中的这个“aaa”对象的地址返回来，赋给引用变量s，这样s会指向String Pool中的这个“aaa”字符串对象
2) 如果存在，则不创建任何对象，直接将String Pool中的这个“aaa”对象地址返回来，赋给s引用。

8、 String s = new String(“aaa”);
1) 首先在String Pool中查找有没有“aaa”这个字符串对象，如果有，则不在String Pool中再去创建“aaa”这个对象了，直接在堆中（heap）中创建一个“aaa”字符串对象，然后将堆中的这个“aaa”对象的地址返回来，赋给s引用，导致s指向了堆中创建的这个“aaa”字符串对象。
2) 如果没有，则首先在String Pool中创建一个“aaa“对象，然后再在堆中（heap）创建一个”aaa“对象，然后将堆中的这个”aaa“对象的地址返回来，赋给s引用，导致s指向了堆中所创建的这个”aaa“对象。

9.new出来的对象都是在堆里面

10.intern（）方法

```
public class Test
{
	public static void main(String[] args)
	{
		Object object = new Object();
		Object object2 = new Object();

		//object类中的equals方法是使用==判断的
		System.out.println(object == object2);//false

		System.out.println("----------------");
		String str = new String("aaa");
		String str2 = new String("aaa");

		System.out.println(str == str2);//false两个对象

		System.out.println("----------------");

		//StringPool字符串池在栈中，当字符串被赋予字面值的时候，首先检查字符串池里面有没有该对象"bbb"
		//如果没有，就将该字符串放入字符串池里面，字符串变量便指向这个对象
		//如果有，就不会在字符串池里面创建新的对象，而是在已有的字符串池里面的对象直接返回来赋给字符串变量str4
		//所以str4并没有创建对象
		//new出来的对象都是在堆里面
		String str3 = "bbb";//创建一个对象
		String str4 = "bbb";//并没有创建对象

		System.out.println(str3 == str4);//true	指向同一个对象
		
		System.out.println("----------------");

		String str5 = new String("ccc");
		String str6 = "ccc";

		System.out.println(str5 == str6);//false

		System.out.println("----------------");

		//字符串的拼接并不是将字符串拼接到后面。字符串是常量，创建后就不能改变了，
		//加法操作实际上是生成了一个新的对象，而不是往原有的对象追加内容。
		String s = "hello";//
		String s1 = "hel";
		String s2 = "lo";

		System.out.println(s == s1 + s2);//false

		System.out.println("----------------");
		System.out.println(s == "hel" + "lo");//true
	}
}

```

```
package testPackage;
class Test {
        public static void main(String[] args) {
                String hello = "Hello", lo = "lo";
                System.out.print((hello == "Hello") + " ");
                System.out.print((Other.hello == hello) + " ");
                System.out.print((other.Other.hello == hello) + " ");
                System.out.print((hello == ("Hel"+"lo")) + " ");
                System.out.print((hello == ("Hel"+lo)) + " ");
                System.out.println(hello == ("Hel"+lo).intern());
        }
}
class Other { static String hello = "Hello"; }

```

###### and the compilation unit:

```
package other;
public class Other { static String hello = "Hello"; }

```

###### produces the output:

true true true true false true

###### This example illustrates six points:

1.Literal strings within the same class (§8) in the same package (§7) represent references to the same String object (§4.3.1).
2.Literal strings within different classes in the same package represent references to the same String object.
3.Literal strings within different classes in different packages likewise represent references to the same String object.
4.Strings computed by constant expressions (§15.28) are computed at compile time and then treated as if they were literals.
5.Strings computed by concatenation at run time are newly created and therefore distinct.

[我的csdn博客](https://blog.csdn.net/prairie97)