---
title: Java中Map和Set的底层分析
date: 2015-06-12 22:02:05
tags:
---



1.HashSet底层是使用HashMap实现的。当使用add方法将对象添加到Set当中时，实际上是将该对象作为底层所维护的Map对象的key，而value则都是同一个Object对象（该对象我们用不上）；其他的都是通过定义的HashMap对象实现的。

2.HashMap的底层，

loadFactor负载因子为0.75，数据结构中的哈希表有关。通过散列函数也就是哈希函数计算。
table是一个Entry类型的数组，当需要的时候回重新调整大小，他的长度必须为2的指数。默认生成一个长度为16的Entry类型的数组。

3.Entry是HashMap的内部类，实现了Map.Entry接口，实现了他的方法。

4.HashMap底层维护一个数组，我们向HashMap中所放置的对象实际上是存放在该数组中。

5.当向HashMap中put一对键值时，它会根据key的hashCode值计算出一个位置，该位置就是此对象准备往数组中存放的位置。
6.如果该位置没有对象存在，就将此对象直接放进数组当中；如果该位置已经有对象存在了，则顺着此存在的对象的链开始寻找（Entry类有一个Entry类型的next成员变量，指向了该对象的下一个对象），如果此链上有对象的话，再去使用equals方法进行比较，如果对此链上的某个对象的equals方法比较为false，则将该对象放到数组当中，然后将数组中该位置以前存在的那个对象链接到此对象的后面。

7.HashMap的内存实现布局：
![Untitled-1-20184222441](http://p693ase25.bkt.clouddn.com/Untitled-1-20184222441.png)

[我的csdn博客](https://blog.csdn.net/prairie97)