---
title: java中的IO流详细解析----字符流
date: 2016-12-15 15:56:06
tags:
---


![这里写图片描述](http://img.blog.csdn.net/20171122204022285?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20171122204035500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


由于Java采用16位的Unicode字符，因此需 要基于字符的输入/输出操作。从Java1.1 版开始，加入了专门处理字符流的抽象类 Reader和Writer，前者用于处理输入，后 者用于处理输出。这两个类类似于 InputStream和OuputStream，也只是提供 一些用于字符流的规定，本身不能用来生 成对象 

 Java程序语言使用Unicode来表示字符串和字符， Unicode使用两个字节来表示一个字符，即一个 字符占16位 


**交换流**

==InputStreamReader和OutputStreamWriter==

这是java.io包中用于处理字符流的基本类， 用来在字节流和字符流之间搭一座“桥”。这 里字节流的编码规范与具体的平台有关， 可以在构造流对象时指定规范，也可以使 用当前平台的缺省规范 




```
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.InputStreamReader;

/**
 * 从控制台输入一句话保存到文件中
 * 文件输出流 将读到的数据写入到本地文件中
 * 转换流 读 输入信息
 *
 */
public class Test3 {
	public static void main(String[] args) throws Exception {
		//1.选择合适的流
		BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
		BufferedWriter bw=new BufferedWriter(new FileWriter("D:\\qf1717\\转换流.txt"));
		//BufferedWriter bw=new BufferedWriter(new OutputStreamWriter(new FileOutputStream("D:\\qf1717\\转换流.txt"),"UTf-8"));
		//2处理数据
		System.out.println("请输入一些话：(输入end结束)");
		String data = br.readLine();
		while(!"end".equals(data)){
			System.out.println("#"+data);
			//写入文件
			bw.write(data);
			//手动换行处理
			bw.newLine();
			//再读
			data=br.readLine();
		}
		//3关闭流
		bw.close();
		br.close();
	}
}
```

InputStreamReader和OutputStreamWriter 类的主要构造方法如下 
- public InputSteamReader(InputSteam in) 
- public InputSteamReader(InputSteam in,String enc) 
- public OutputStreamWriter(OutputStream out) 
- public OutputStreamWriter(OutputStream out,String enc)  

• 其中in和out分别为输入和输出字节流对象， enc为**指定的编码规范**（若无此参数，表示 使用当前平台的缺省规范，可用 getEncoding()方法得到当前字符流所用 的编码方式）。 


**文件输入输出流**

==FileWriter和FileReader==


注意：
 FileWriter(String filePath, boolean append)  
 
 append ：如果为 true，则将字节写入文件末 尾处，而不是写入文件开始处，默认为false 
 

```
import java.io.BufferedReader;
import java.io.FileReader;

public class FileReader1
{
	public static void main(String[] args) throws Exception
	{
		FileReader fr = new FileReader(
				"c:\\FileReader1.java");

		BufferedReader br = new BufferedReader(fr);

		String str;

		while (null != (str = br.readLine()))
		{
			System.out.println(str);
		}

		br.close();
	}
}

```

```

import java.io.FileWriter;

public class FileWriter1
{
	public static void main(String[] args) throws Exception
	{
		String str = "hello world welcome nihao hehe";
		
		char[] buffer = new char[str.length()];
		
		str.getChars(0, str.length(), buffer, 0);
		
		FileWriter f = new FileWriter("file2.txt");
		
		for(int i = 0; i < buffer.length; i++)
		{
			f.write(buffer[i]);
		}
		
		f.close();
		
	}
}

```


字符数组流

==CharArrayReader和CharArrayWriter==

```
import java.io.CharArrayReader;

public class CharArrayReader1
{
	public static void main(String[] args) throws Exception
	{
		String tmp = "abcdefg";
		
		char[] ch = new char[tmp.length()];
		
		tmp.getChars(0, tmp.length(), ch, 0);
		
		CharArrayReader input = new CharArrayReader(ch);
		
		int i;
		
		while(-1 != (i = input.read()))
		{
			System.out.println((char)i);
		}
	}
}

```

缓冲字符流
BufferWriter和BufferReader

和字节流用法一致，我这里不在举例说明。


Properties类

主要是用于配置文件，这里简单的说一下。

举例：


```

import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Properties;

public class PropertiesUserTest {
  public static void main(String[] args) throws FileNotFoundException, IOException {
	  //1.实例一个Properties对象
	  Properties info=new Properties();
	  //2.将User.properties中的键值对 添加入info集合中
	 /* void load(InputStream inStream) 
      从输入流中读取属性列表（键和元素对）。 */
	  info.load(new BufferedInputStream(new FileInputStream(new File("file\\User.properties"))));
      System.out.println(info);
      
      //3.在info中添加一对新的键值对
     /* Object setProperty(String key, String value) 
      调用 Hashtable 的方法 put。 */
      info.setProperty("address", "China");
      System.out.println(info);
      
      //4.将集合中的新添加的键值对同步到配置文件中
      /*void store(OutputStream out, String comments) 
      以适合使用 load(InputStream) 方法加载到 Properties 
      表中的格式，将此 Properties 表中的属性列表（键和元素对）写入输出流。 */
      //comments--属性列表的描述
      info.store(new BufferedOutputStream (new FileOutputStream(new File("file\\User.properties" ))), 
    		  "add a pair of key and value");
      
  }
}

```

IO流的内容比较多，但是不是很难，希望大家能够看懂例子。


[csdn博客相关内容](https://blog.csdn.net/prairie97)