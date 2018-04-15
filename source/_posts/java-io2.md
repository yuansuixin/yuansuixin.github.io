---
title: Java中的IO流详细讲解----字节流
date: 2018-08-15 15:55:59
tags:
---


![这里写图片描述](http://img.blog.csdn.net/20171122201546188?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20171122201608552?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
##### 字节流

###### 文件输入输出流

==FileInputStream和FileOutputStream==

 Java提供了一系列的读写文件的类和方法。在Java 中，所有的文件都是字节形式的。Java提供从文 件读写字节的方法。而且，Java允许在字符形式 的对象中使用字节文件流。
 
 两个最常用的流类是FileInputStream和 FileOutputStream，它们生成与文件链接的字节流。 为打开文件，你只需创建这些类中某一个类的一 个对象，在构造方法中以参数形式指定文件的名称

当你对文件的操作结束后，需要调用close( )来关闭文件。 


读取音频，图片等数据的时候只能使用字节流，下面来看一下使用文件流实现复制的功能。（这里主要说IO流，对于异常我直接抛出，后面不在提及）


```
package com.qf.byteio.test;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

/**
 * 功能：通过字节输入输出流，完成文件copy  InputStream OutputStream
 * 分析：
 *    源文件                       程序                       目标文件
 *    1.找到源文件
 *    2.读取源文件里的信息  输入 程序
 *    3.将程序读到的数据  写入   源文件
 * 
 *
 */
public class TestCopy3 {
	public static void main(String[] args) throws IOException {
		//1.选择合适流 InputStream OutputStream
		FileInputStream fis=new FileInputStream(new File("D:\\qf1717\\Wildlife.wmv"));
		FileOutputStream fos=new FileOutputStream(new File("D:\\qf1717\\Wildlife(复制).wmv")); 
		//2.处理数据
		    //2.1 创建缓存区
		    byte [] buf=new byte[1024];
		    //2.2从缓存区读数据
		    int len = fis.read(buf);
		    while(len!=-1){
		    	//将读的数据写入 新文件中
		    	fos.write(buf);
		    	len=fis.read(buf);//注意点  len再次赋值  读 缓存区的数据
		    }
		//3.关流
		fos.close();
		fis.close();
		System.out.println("复制完成");
	}
}
```

文件的复制例子1：（一个字符一个字符的读）

```

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;

/**
 * 功能：通过字节输入输出流，完成文件copy  InputStream OutputStream
 * 分析：
 *    源文件                       程序                       目标文件
 *    1.找到源文件
 *    2.读取源文件里的信息  输入 程序
 *    3.将程序读到的数据  写入   源文件
 *    
 * 第一步：找到源文件    读取源文件里的信息  输入 程序
 *       将程序读到内容打印到控制台
 *       流的操作
 *          1选择合适的流（流的分类）
 *          2处理数据
 *          3.关流
 *
 *
 */
public class TestInputStream2 {
  public static void main(String[] args) throws IOException {
	  //找到源文件    读取源文件里的信息  输入 程序  将程序读到内容打印到控制台
	  //1选择合适的流（流的分类）
	  File f=new File("D:\\qf1717\\abc.txt");
	  //File f=new File("D:/qf1717/abc.txt");
	  InputStream fis=new FileInputStream(f);
	  //2处理数据
	 
	  /*int n=fis.read();//97
	  while(n!=-1){
		  System.out.print((char)n);
		  n=fis.read();//迭代式 -1
	  }*/
	  int n=0;
	  while((n=fis.read())!=-1){//97
		  System.out.print((char)n);
	  }
	  //3关流
	  fis.close();
  }
}
```
文件的复制例子2：（使用临时处理区，多个字符的读）

```
package com.qf.byteio.test;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.util.Arrays;

/**
 * 功能：通过字节输入输出流，完成文件copy  InputStream OutputStream
 * 分析：
 *    源文件                       程序                       目标文件
 *    1.找到源文件
 *    2.读取源文件里的信息  输入 程序
 *    3.将程序读到的数据  写入   源文件
 *    
 * 第一步：找到源文件    读取源文件里的信息  输入 程序
 *       将程序读到内容打印到控制台
 *       流的操作
 *          1选择合适的流（流的分类）
 *          2处理数据
 *          3.关流
 *
 *
 */
public class TestInputStream4 {
  public static void main(String[] args) throws IOException {
	  //找到源文件    读取源文件里的信息  输入 程序  将程序读到内容打印到控制台
	  //1选择合适的流（流的分类）
	  File f=new File("D:\\qf1717\\abc.txt");
	  //File f=new File("D:/qf1717/abc.txt");
	  InputStream fis=new FileInputStream(f);
	  //2处理数据
	  //2.1 创建一个临时区域 一次性存多个值
	     byte [] buf=new byte[1024];//abcdef
	  //2.2从临时区域读数据
	     /*int len=fis.read(buf);
	     //System.out.println(len);//字节数
	     //System.out.println(Arrays.toString(buf));
	     System.out.println(new String(buf));
	     System.out.println(new String(buf,0,len));//0 5
*/	     
	     int len=fis.read(buf);
	     while(len!=-1){
	    	 System.out.println(new String(buf,0,len));
	    	 len=fis.read(buf);
	     }
	  
	  //3关流
	  fis.close();
  }
}
```
available( )判定剩余的字节个数及怎样用，skip( )方法跳过不必要的字节

文件的复制例子2：（使用临时处理区，多个字符的读）
```
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

/**
 * 功能：通过字节输入输出流，完成文件copy  InputStream OutputStream
 * 分析：
 *    源文件                       程序                       目标文件
 *    1.找到源文件
 *    2.读取源文件里的信息  输入 程序
 *    3.将程序读到的数据  写入   源文件
 * 
 * 将程序读到的数据写入目标文件
 */
public class TestOutputStream2 {
	public static void main(String[] args) throws IOException {
		 //1.选择合适的流
		 FileOutputStream fos=new FileOutputStream(new File("D:\\qf1717\\byte写1.txt"));
		 //2.处理数据
		  String s="abcdefghijklmn";
		  byte[] buf = s.getBytes();
		  fos.write(buf);
		 //3.关闭流
		  fos.close();
		  System.out.println("程序结束");
	}
}

```
**注意**：
文件输出流有个构造方法两个参数，
 public FileOutputStream(File file, boolean append)

file - 为了进行写入而打开的文件。
append - 如果为 true，则将字节写入文件末尾处，而不是写入文件开始处 ，默认是false


###### 字节数组输入输出流

==ByteArrayInputStream和ByteArrayOutputStream==

字节数组输入输出流又称为内存流，下面通过内存流实现图片的复制


```
package com.qf.bytearrayio.test;

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

/**
 * 文件字节输入流  文件字节输出流  字节数组输入流  字节数组输出流
 * 功能：借助 内存流完成图片copy
 *    分析：
 *       文件字节输入流  找到资源文件  read 
 *       字节数组输出流  写
 *       程序
 *        字节数组输入流 读
 *         文件字节输出流 写
 */
public class TestCopy {
	public static void main(String[] args) throws IOException {
		//创建流的对象
		FileInputStream fis=new FileInputStream(new File("Koala.jpg"));
		ByteArrayOutputStream bos=new ByteArrayOutputStream();
		//处理数据
		byte [] buf=new byte[1024];
		int len=fis.read(buf);
		while(len!=-1){
			//写
			bos.write(buf, 0, len);
			//再读
			len=fis.read(buf);
		}
		System.out.println("写入内存流中成功");
		
		byte[] buf1 = bos.toByteArray();//将内存流中的数据取出形参字节数组
		//读的流的对象
		ByteArrayInputStream bis=new ByteArrayInputStream(buf1);
		FileOutputStream fos=new FileOutputStream(new File("Koala(复制).jpg"));
		//处理数据
		/*int n = bis.read();
		while(n!=-1){
			fos.write(n);
			n=bis.read();
		}*/
		int n=0;
		while((n=bis.read())!=-1){
			fos.write(n);
		}
		//关闭流
		fos.close();
		bis.close();
		bos.close();
		fis.close();
		System.out.println("复制完成");
	}
}
```
**注意**：
- writeTo方法:将此字节数组输出流的全部内容写 入到指定的输出流参数中
- reset:将此字节数组输出流的 count 字段重置 为零，从而丢弃输出流中目前已累积的所有输 出。通过重新使用已分配的缓冲区空间，可以再次使用该输出流


使用内存流实现数据的读写操作：
```
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;

/**
 * 字节数组输入流 ：
 *    ByteArrayInputStream:内存流
 *                         读
 *   String.getBytes():  String ------>byte[]
 * 字节数组输出流：
 *    ByteArrayOutputStream:内存流
 *                          写
 *                bos.toByteArray();将内存中数据取出来 转换为字节数组
 */
public class Test2 {
	public static void main(String[] args) throws IOException {
		//1.选择合适的流
		ByteArrayOutputStream bos=new ByteArrayOutputStream();
		//向字节数组输出流写入数据
		bos.write("nihao123".getBytes());
		System.out.println("写入成功");
		//2.处理数据
		//将内存流的数据转换为字节数组 byte[]**
		byte[] buf = bos.toByteArray();
		System.out.println(buf.length);//nihao123
		ByteArrayInputStream bis=new ByteArrayInputStream(buf);//byte[]
		//从内存数组输入流读到数据 打印到控制台
		byte [] buf1=new byte[1024];
		int len = bis.read(buf1);
		while(len!=-1){
			System.out.println(new String(buf1,0,len));
			len=bis.read(buf1);
		}
		//3关闭流
		bos.close();
		bis.close();
		System.out.println("程序结束");
		
	}
}

```

##### 缓冲流

==BufferedInputStream和BufferedOutputStream==

实现图片的复制：

```
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

public class TestCopy {
	public static void main(String[] args)  {
		//1.选择合适的流
		BufferedInputStream bis=null;
		BufferedOutputStream bos=null;
		try {
			bis = new BufferedInputStream(new FileInputStream("D:\\qf1717\\Koala.jpg"));
			bos = new BufferedOutputStream(new FileOutputStream("D:\\qf1717\\Koala复制(BUF).jpg"));
			//2.处理数据
			  byte [] buf=new byte[1024];
			  int len=bis.read(buf);
			  while(len!=-1){
				  bos.write(buf, 0, len);//保证最后一次读取数据有效
				  len=bis.read(buf);
			  }
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally{
			if(bos!=null){
				//3.关闭流
				  try {
					bos.close();
				} catch (IOException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
				  
			}
			if(bis!=null){
				try {
					bis.close();
				} catch (IOException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
		}
		
		  System.out.println("复制完成");
	}
}

```
**注意**：
- public BufferedInputStream(InputStream in,int size)这个构造方法中size默认的大小是8192
- 用flush()方法更新流，要想在程序结束之前将缓冲区里的数据写 入磁盘，除了填满缓冲区或关闭输出流外， 还可以显式调用flush()方法


###### 处理流

==DataInputStream和DataOutputStream==

```
package com.qf.dataio.test;

import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

/**
 * DataInputStream  DataOutputStream
 *   里面提供存储java基本数据类型： 四类八种   String
 *   处理流  只针对字节流 （二进制文件）
 *  IOException 的子类  java.io.EOFException
 *    EOF：End of File
 */
public class Test1 {
	public static void main(String[] args) throws IOException {
		//write();
		//read();
		System.out.println(System.getProperties());
		
	}
	public static void write() throws IOException{
		//1.选择合适的流
		FileOutputStream fos=new FileOutputStream(new File("D:\\qf1717\\DataIO.txt"));
		BufferedOutputStream bos=new BufferedOutputStream(fos);
		DataOutputStream dos=new DataOutputStream(bos);
		//2.处理数据
		  //基本数据类型
		  dos.writeInt(12);
		  dos.writeDouble(34.5);
		  dos.writeChar('A');
		  dos.writeBoolean(true);
		  
		  
		  //处理字符串
		  dos.writeUTF("我爱你中国");
		//3.关闭流
		  dos.close();
	}
	public static void read() throws IOException{
		//1.选择合适的流
		FileInputStream fis=new FileInputStream(new File("D:\\qf1717\\DataIO.txt"));
		BufferedInputStream bis=new BufferedInputStream(fis);
		DataInputStream dis=new DataInputStream(bis);
		//2.处理数据
		  //基本数据类型
		System.out.println(dis.readInt());
		System.out.println(dis.readDouble());
		System.out.println(dis.readChar());
		System.out.println(dis.readBoolean());
		
		  //String
		System.out.println(dis.readUTF());
		//3.关闭流
		dis.close();
	}
}

```

###### 打印流

==PrintStream==

功能：键盘录入信息 通过打印流 将数据保存到文件中
```
import java.io.BufferedReader;
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;

public class Test2 {
	public static void main(String[] args) throws IOException {
		//1.选择合适的流
		BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
		PrintWriter pw=new PrintWriter(new FileWriter(new File("D:\\qf1717\\123(打印流).txt")));
		//2.处理数据
		String data = br.readLine();
		while(data!=null){
			pw.write(data);
			pw.flush();
			data=br.readLine();
		}
		//3关闭流
		pw.close();
		br.close();
		
	}
}

```
###### 对象流

==ObjectInputStream和ObjectOutputStream==

这里涉及到对象的序列化和反序列化，这里我来简单的介绍一下。


序列化

将对象转换为字节流保存起来，并在以后 还原这个对象，这种机制叫做对象序列化，将一个对象保存到永久存储设备上称为持久化， 一个对象要想能够实现序列化，必须实现 Serializable接口或Externalizable接口。

序列化（serialization）是把一个对象的状态写入 一个字节流的过程。当你想要把你的程序状态存 到一个固定的存储区域例如文件时，它是很管用 的。稍后一点时间，你就可以运用序列化过程存储这些对象  

 假设一个被序列化的对象引用了其他对象，同样，其他对象又引用了更多的对象对象 和它们的关系形成了一个顺序图表。图表中也有循环引用。也就是说，对象X可以含有一个对象Y的引用，对象Y同样可以包含一个对象X的引用。对象同样可以包含它们自己的引用。对象序列化和反序列化的工具被设计出来并在这 一假定条件下运行良好。如果你试图序列化一个 对象图表中顶层的对象，所有的其他的引用对象 都被循环的定位和序列化。同样，在反序列化过 程中，所有的这些对象以及它们的引用都被正确 的恢复  
 
• 当一个对象被序列化时，只保存对象的非静态成 员变量，不能保存任何的成员方法和静态的成员 变量。

• 如果一个对象的成员变量是一个对象，那么这个 对象的数据成员也会被保存。

• 如果一个可序列化的对象包含对某个不可序列化 的对象的引用，那么整个序列化操作将会失败， 并且会抛出一个NotSerializableException。我 们可以将这个引用标记为transient，那么对象仍 然可以序列化 

**Serializable接口** 
- 只有一个实现Serializable接口的对象可以被 序列化工具存储和恢复。Serializable接口没 有定义任何成员。它只用来表示一个类可以被 序列化。如果一个类可以序列化，它的所有子 类都可以序列化。
- 声明成transient的变量不被序列化工具存储。 同样，static变量也不被存储 



 Externalizable接口 
 - Java的序列化和反序列化的工具被设计出来，所以很多存 储和恢复对象状态的工作自动进行。然而，在某些情况下， 程序员必须控制这些过程。例如，在需要使用压缩或加密 技术时，Externalizable接口为这些情况而设计。 
 
Externalizable 接口定义了两个方法：
 
 -  – void readExternal(ObjectInput inStream)      throws IOException, ClassNotFoundException
 -  – void writeExternal(ObjectOutput outStream)      throws IOException 
 
这些方法中，inStream是对象被读取的字节流，outStream是 对象被写入的字节流。 
 
• Externalizable 实例类的惟一特性是可以 被写入序列化流中，该类负责保存和恢复 实例内容。 若某类要完全控制某一对象及 其超类型的流格式和内容，则它要实现 Externalizable 接口的 writeExternal 和 readExternal 方法。这些方法必须显式与 超类型进行协调以保存其状态。这些方法 将代替自定义的 writeObject 和 readObject 方法实现 


接下来我们来看个例子：

```
package com.qf.objectio.test;

import java.io.Serializable;

public class Student implements Serializable{
   private int sid;
   private String name;
   private int age;
   public Student() {
		super();
	}
	public Student(int sid, String name, int age) {
		super();
		this.sid = sid;
		this.name = name;
		this.age = age;
	}
	public int getSid() {
		return sid;
	}
	public void setSid(int sid) {
		this.sid = sid;
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
	@Override
	public String toString() {
		return "Student [sid=" + sid + ", name=" + name + ", age=" + age + "]";
	}
   
}

```


```
package com.qf.objectio.test;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

/**
 * 对象流
 * 对象序列化： 对象数据   -------》 文件 （二进制文件）
 * 对象反序列化：文件中的数据----->对象 
 *  java.io.NotSerializableException: com.qf.objectio.test.Student
 *  
 *  联通  UTF-8**
 */
public class TestStudent {
	public static void main(String[] args) throws IOException, ClassNotFoundException {
		//writeObject();
		readObject();
	}
	public static void writeObject() throws IOException{
		//1.选择合适的流
		FileOutputStream fos=new FileOutputStream(new File("D:\\qf1717\\对象流.txt"));
		ObjectOutputStream oos=new ObjectOutputStream(fos);
		//2.处理数据
		Student stu1=new Student(1001,"aa",17);
		oos.writeObject(stu1);//Object
		//3.关闭流
		oos.close();
		System.out.println("完成");
	}
	public static void readObject() throws IOException, ClassNotFoundException{
		//1.选择合适的流
		FileInputStream fis=new FileInputStream(new File("D:\\qf1717\\对象流.txt"));
		ObjectInputStream ois=new ObjectInputStream(fis);
		//2.处理数据
		Student stu=(Student)ois.readObject();
		System.out.println(stu);
		System.out.println(stu.getSid()+"\t"+stu.getName()+"\t"+stu.getAge());
		//3.关闭流
		ois.close();
	}
}
```
那么当我们可序列化的对象引用了其他的对象的时候结果怎样呢，我们继续来看


```

import java.io.Serializable;

public class Grade implements Serializable {
    /**
	 * 
	 */
	private static final long serialVersionUID = 7208450044584117263L;
	private int gid;
    private String gname;
	public Grade(int gid, String gname) {
		super();
		this.gid = gid;
		this.gname = gname;
	}
	public Grade() {
		super();
	}
	public int getGid() {
		return gid;
	}
	public void setGid(int gid) {
		this.gid = gid;
	}
	public String getGname() {
		return gname;
	}
	public void setGname(String gname) {
		this.gname = gname;
	}
	@Override
	public String toString() {
		return "Grade [gid=" + gid + ", gname=" + gname + "]";
	}
    
}
```

```
package com.qf.objectio.test;

import java.io.Serializable;

public class Student2 implements Serializable{
   /**
	 * 
	 */
	private static final long serialVersionUID = 1L;
private int sid;
   private static String name="张三";
   private transient int age=18;//瞬时属性
   private Grade grade;
   
   public Student2() {
		super();
	}
	public Student2(int sid, String name, int age) {
		super();
		this.sid = sid;
		this.name = name;
		this.age = age;
	}
	
	public Student2(int sid, String name, int age, Grade grade) {
		super();
		this.sid = sid;
		this.name = name;
		this.age = age;
		this.grade = grade;
	}
	
	public Grade getGrade() {
		return grade;
	}
	public void setGrade(Grade grade) {
		this.grade = grade;
	}
	public int getSid() {
		return sid;
	}
	public void setSid(int sid) {
		this.sid = sid;
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
	@Override
	public String toString() {
		return "Student [sid=" + sid + ", name=" + name + ", age=" + age + "]";
	}
   
}

```


```
package com.qf.objectio.test;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
/**
 * 
 * java.io.NotSerializableException: com.qf.objectio.test.Grade
 *
 */
public class TestStudent2 {
	public static void main(String[] args) throws IOException, ClassNotFoundException {
		writeObject();
		readObject();
	}
	/**
	 * 序列化
	 * @throws IOException
	 */
	public static void writeObject() throws IOException{
		//1.选择合适流
		FileOutputStream fos=new FileOutputStream(new File("D:\\qf1717\\对象流2.txt"));
		ObjectOutputStream oos=new ObjectOutputStream(fos);
		//2处理数据
		Grade g=new Grade(1,"1717");
		Student2 stu1=new Student2(1001,"aa",17,g);
		oos.writeObject(stu1);
		//3.关闭流
		oos.close();
		System.out.println("完成");
	}
	/**
	 * 反序列化
	 * @throws IOException
	 * @throws ClassNotFoundException 
	 */
	public static void readObject() throws IOException, ClassNotFoundException{
		//1.选择合适的流
		FileInputStream fis=new FileInputStream(new File("D:\\qf1717\\对象流2.txt"));
		ObjectInputStream ois=new ObjectInputStream(fis);
		//2.处理数据
		Student2 stu1=(Student2)ois.readObject();
		System.out.println(stu1);
		System.out.println(stu1.getGrade().getGid());
		System.out.println(stu1.getGrade().getGname());
		//3.关闭流
		ois.close();
	}
}

```


```
package com.qf.objectio.test;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

/**
 * 对象流
 * 对象序列化： 对象数据   -------》 文件 （二进制文件）
 * 对象反序列化：文件中的数据----->对象 
 *  java.io.NotSerializableException: com.qf.objectio.test.Student
 *  
 *  联通  UTF-8**
 */
public class TestStudent3 {
	public static void main(String[] args) throws IOException, ClassNotFoundException {
		//writeObject();
		readObject();
	}
	public static void writeObject() throws IOException{
		//1.选择合适的流
		FileOutputStream fos=new FileOutputStream(new File("D:\\qf1717\\对象流.txt"));
		ObjectOutputStream oos=new ObjectOutputStream(fos);
		//2.处理数据
		Student stu1=new Student(1001,"aa",17);
		Student stu2=new Student(1002,"aa",18);
		Student stu3=new Student(1003,"aa",19);
		Student stu4=new Student(1004,"aa",17);
		Student stu5=new Student(1005,"aa",16);
		oos.writeInt(5);
		oos.writeObject(stu1);//Object
		oos.writeObject(stu2);//Object
		oos.writeObject(stu3);//Object
		oos.writeObject(stu4);//Object
		oos.writeObject(stu5);//Object
		//3.关闭流
		oos.close();
		System.out.println("完成");
	}
	public static void readObject() throws IOException, ClassNotFoundException{
		//1.选择合适的流
		FileInputStream fis=new FileInputStream(new File("D:\\qf1717\\对象流.txt"));
		ObjectInputStream ois=new ObjectInputStream(fis);
		//2.处理数据
		/*Student stu1=(Student)ois.readObject();
		System.out.println(stu1);
		Student stu2=(Student)ois.readObject();
		System.out.println(stu2);
		Student stu3=(Student)ois.readObject();
		System.out.println(stu3);
		Student stu4=(Student)ois.readObject();
		System.out.println(stu4);
		Student stu5=(Student)ois.readObject();
		System.out.println(stu5);*/
		
		int count = ois.readInt();
		for(int i=0;i<count;i++){
			Student stu=(Student)ois.readObject();
			System.out.println(stu);
		}
		//3.关闭流
		ois.close();
	}
}

```

如果边读边写，那么静态变量为输入的数据，瞬时变量为默认值，如果写完之后再读，那么静态变量是初始值，瞬时变量为默认值。

反序列化时不会调用对象的任何构造方法，仅 仅根据所保存的对象状态信息，在内存中重新 构建对象 

**小结**：
1.	一个类若想被序列化，则需要实现java.io.Serializable接口，该接口中没有定义任何方法，是一个标识性接口（Marker Interface），当一个类实现了该接口，就表示这个类的对象是可以序列化的。

2.	在序列化时，static变量是无法序列化的；如果A包含了对B的引用，那么在序列化A的时候也会将B一并地序列化；如果此时A可以序列化，B无法序列化，那么当序列化A的时候就会发生异常，这时就需要将对B的引用设为transient，该关键字表示变量不会被序列化。
 
3.	当我们在一个待序列化/反序列化的类中实现了以上两个private方法（方法声明要与上面的保持完全的一致），那么就允许我们以更加底层、更加细粒度的方式控制序列化/反序列化的过程。




[csdn博客相关内容](https://blog.csdn.net/prairie97)









