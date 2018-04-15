---
title: Java中的IO流总结（1）
date: 2018-07-15 15:52:34
tags:
---


IO流这块知识点比较多而且琐碎，今天我就来总结一下IO流的知识点。

#### File类
---

File类的直接父类是Object类。

下面的**构造方法**可以用来生成File 对象：
- File(String directoryPath) 
- File(String directoryPath, String filename) 
- File(File dirObj, String filename) 

这里，directoryPath是文件的路径名， filename 是文件名，dirObj 是一个指定目录的File 对象.


**目录操作的主要方法**为： 
- public boolean mkdir() 根据抽象路径名创建目录。 
- public String[] list() 返回抽象路径名表示路径中 的文件名和目录名。 


File类是++不对称++的。说它不对 称，意思是虽然存在允许验证一个简单文 件对象属性的很多方法，但是没有相应的 方法来改变这些属性


 **File类中的常用方法** 
 - String getName() 
 - String getPath() 
 - String getAbsolutePath()
 - String getParent()
 - boolean renameTo( File newName)
 - long length()
 - boolean delete() 
 - boolean mkdir()
 - String[] list()
- boolean exists() 
- boolean canWrite() 
- boolean canRead()
- boolean isFile()
-boolean isDirectory()
- File[ ] listFiles()

**路径分隔符**

Windows里面的\表示转义，在程序中需要使用双反斜杠\,或者是使用/，其他的系统都是使用的/。


#### 流类


---


 - 输入/输出时，数据在通信通道中流动。所谓“数 据流(stream)”指的是所有数据通信通道之中， 数据的起点和终点。信息的通道就是一个数据流。 只要是数据从一个地方“流”到另外一个地方， 这种数据流动的通道都可以称为数据流。 
 - 输入/输出是相对于程序来说的。程序在使用数据 时所扮演的角色有两个：一个是源，一个是目的。 若程序是数据流的源，即数据的提供者，这个数 据流对程序来说就是一个“输出数据流”(数据从 程序流出)。若程序是数据流的终点，这个数据流 对程序而言就是一个“输入数据流” ( 数据从程 序外流向程序)


Java 2 定义了两种类型的流：
**字节流和字符流**。

**字节流**（byte stream）为处理字 节的输入和输出提供了方便的方法。例如 使用字节流读取或写入二进制数据。**字符流**（character stream）为字符的输入和 输出处理提供了方便。它们采用了统一的 编码标准，因而可以国际化。当然，在某 些场合，字符流比字节流更有效


==在最底层，所有的输入/输出都 是字节形式的==。基于字符的流只为处理字符提供方便有效的方法.


- 字节流类（Byte Streams） 字节流类用于 向字节流读写8位二进制的字节。一般地， 字节流类主要用于读写诸如图象或声音等 的二进制数据。
- 字符流类（Character Streams） 字符流 类用于向字符流读写16位二进制字符



###### 流的分类

1. 两种基本的流是：输入流(Input Stream) 和输出流(Output Stream)。可从中读出 一系列字节的对象称为输入流。而能向其 中写入一系列字节的对象称为输出流。

2.节点流：从特定的地方读写的流类，例如：磁盘或一块内存区域。 过滤流：使用节点流作为输入或输出。过滤流是使用一个已经存在的 输入流或输出流连接创建的。


##### 字节流

字节流类以InputStream 和 OutputStream为顶层类,他们都是抽象类 (abstract)，抽象类InputStream 和 OutputStream定义了实现其 他流类的关键方法。最重要的两种方法是read()和write()，它们分别对数据的字节进行读写。两种 方法都在InputStream 和OutputStream中被定义为 抽象方法。它们被派生的流类重写。每个抽象类都有多个具体的子类，这些子类对不同的外设进行处理，例如磁盘文件，网络连接，甚至是内存缓冲区。 要使用流类，必须导入java.io包。


 三个基本的读方法 
 - abstract int read() ：读取一个字节数据， 并返回读到的数据，如果返回-1，表示读到了输 入流的末尾。 
 - int read(byte[] b) ：将数据读入一个字节 数组，同时返回实际读取的字节数。如果返回-1， 表示读到了输入流的末尾。
 - int read(byte[] b, int off, int len) ：将数 据读入一个字节数组，同时返回实际读取的字节 数。如果返回-1，表示读到了输入流的末尾。off 指定在数组b中存放数据的起始偏移位置；len指 定读取的最大字节数。

为什么只有第一个read方法是抽象 的，而其余两个read方法都是具体的？
- 因为第二个read方法依靠第三个read方法 来实现，而第三个read方法又依靠第一个 read方法来实现，所以说只有第一个read 方法是与具体的I/O设备相关的，它需要 InputStream的子类来实现


其它方法 
- long skip(long n) ：在输入流中跳过n个字节，并返 回实际跳过的字节数。 
- int available() ：返回在不发生阻塞的情况下，可读 取的字节数。
- void close() ：关闭输入流，释放和这个流相关的系 统资源。
- void mark(int readlimit) ：在输入流的当前位置放 置一个标记，如果读取的字节数多于readlimit设置的值， 则流忽略这个标记。 
- void reset() ：返回到上一个标记。 
- boolean markSupported() ：测试当前流是否支持 mark和reset方法。如果支持，返回true，否则返回 false。



 - InputStream中包含一套字节输入流需要的方法， 可以完成最基本的从输入流读入数据的功能。当 Java程序需要外设的数据时，可根据数据的不同 形式，创建一个适当的InputStream子类类型的对 象来完成与该外设的连接，然后再调用执行这个 流类对象的特定输入方法来实现对相应外设的输 入操作。
 - InputStream 类 子 类 对 象 自 然 也 继 承 了 InputStream类的方法。常用的方法有：读数据的 方法read() ， 获取输入流字节数的方法 available()，定位输入位置指针的方法skip()、 reset()、mark()等。


 三个基本的写方法:
 - abstract void write(int b) ：往输出 流中写入一个字节。 
 - void write(byte[] b) ：往输出流中写 入数组b中的所有字节。
 - void write(byte[] b, int off, int len) ：往输出流中写入数组b中从偏移 量off开始的len个字节的数据


其它方法 
- void flush() ：刷新输出流，强制缓冲 区中的输出字节被写出。 
- void close() ：关闭输出流，释放和这 个流相关的系统资源。



##### 过滤流

在InputStream类和OutputStream类子类中， FilterInputStream 和 FilterOutputStream 过滤流抽象类又派生出DataInputStream和 DataOutputStream数据输入输出流类等子 类。

过滤流的主要特点是在输入输出数据的同时能对所传输的 数据做指定类型或格式的转换，即可实现对二进制字节数 据的理解和编码转换。
- 数据输入流DataInputStream中定义了多个针对不同类型 数 据 的 读 方 法 ， 如 readByte() 、 readBoolean() 、 readShort()、readChar()、readInt()、readLong()、 readFloat()、readDouble()、readLine()等。 
- 数据输出流DataOutputStream中定义了多个针对不同类型 数 据 的 写 方 法 ， 如 writeByte() 、 writeBoolean() 、 writeShort()、writeChar()、writeInt()、writeLong()、 writeFloat()、writeDouble()、writeChars()等。
- 过滤流在读/写数据的同时可以对数据进行 处理，它提供了同步机制，使得某一时刻 只有一个线程可以访问一个I/O流，以防止 多个线程同时对一个I/O流进行操作所带来 的意想不到的结果。 
- 类FilterInputStream和 FilterOutputStream分别作为所有过滤输 入流和输出流的父类。


###### 基本的流类
- FileInputStream和FileOutputStream 节点流，用于从文件中读取或往文件中写入字节流。如果在构造 FileOutputStream时，文件已经存在，则覆盖这个文件。
- BufferedInputStream和 BufferedOutputStream 过滤流，需要使用已经存在的节点流来构造，提供带缓冲的读写，提高了读写的效率。
- DataInputStream和DataOutputStream 过滤流，需要使用已经存在的节点流来构造，提供了读写Java中的基本数 据类型的功能。 
-  PipedInputStream和PipedOutputStream 管道流，用于线程间的通信。一个线程的PipedInputStream对象 从另一个线程的PipedOutputStream对象读取输入。要使管道流有 用，必须同时构造管道输入流和管道输出流。


java的IO库的设计原则是利用的装饰设计模式，前面我介绍过了。



[装饰设计模式](http://blog.csdn.net/prairie97/article/details/78599420)


##### 预定义流

所有的Java程序自动导入java.lang包。该包 定义了一个名为System的类，该类封装了运 行时环境的多方面信息。例如，使用它的 某些方法，你能获得当前时间和与系统有 关的不同属性。System 同时包含三个预定 义的流变量，in，out和err。这些成员在 System中是被定义成public 和static型的，这 意味着它们可以不引用特定的System对象而 被用于程序的其他部分。


System.out是标准的输出流。默认情况下，它是一 个控制台。System.in是标准输入，默认情况下， 它指的是键盘。System.err指的是标准错误流，它 默认是控制台。然而，这些流可以重定向到任何 兼容的输入/输出设备 • System.in 是InputStream的对象；System.out和 System.err是PrintStream的对象。它们都是字节流， 尽管它们用来读写外设的字符。如果愿意，你可 以用基于字符的流来包装它们
 
• System类的类变量in表示标准输入流，其 定义为：
- public static final InputStream in 
-  标准输入流已打开，作好提供输入数据的 准备。一般这个流对应键盘输入，可以使 用InputStream 类的read()和skip(long n)等方法来从输入流获得数据。read()从 输入流中读一个字节，skip(long n)在输 入流中跳过n个字节。

System类的类变量out表示标准输出流，其定义 为：
- public static final PrintStream out
- 标准输出流也已打开，作好接收数据的准备。一 般这个流对应显示器输出。可以使用PrintStream 类的print()和println() 等方法来输出数据，这两个方法支持Java的任意 基本类型作为参数。

System类的类变量err表示标准错误输出 流，其定义为：
- public static final PrintStream err
- 标准错误输出流已打开，作好接收数据的 准备。一般这个流也对应显示器输出，与 System.out一样，可以访问PrintStream 类的方法。



[csdn博客相关内容](https://blog.csdn.net/prairie97)

