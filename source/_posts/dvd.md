---
title: Java小项目----DVD管理系统
date: 2017-02-07 20:14:24
tags:
---




做了一个DVD的管理系统，包含了增删改查\借出和归还等功能和日期与字符串的装换问题，使用了最简单的java基础知识，封装了方法，属性，实现了基本的功能。这个系统同样适用于图书馆管理系统，里面也实现了借出和归还的功能。

下面给大家分析一下这个系统：

- 用例1：数据初始化
- 用例2：实现菜单切换
- 用例3：实现查看DVD信息
- 用例4：实现新增DVD信息
- 用例5：实现删除DVD信息
- 用例6：实现借出DVD业务处理
- 用例7：实现归还DVD业务处理

![这里写图片描述](http://img.blog.csdn.net/20171120101502937?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


![这里写图片描述](http://img.blog.csdn.net/20171120101517209?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


![4](http://img.blog.csdn.net/20171120101539244?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![5](http://img.blog.csdn.net/20171120101554328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


![6](http://img.blog.csdn.net/20171120101638591?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![7](http://img.blog.csdn.net/20171120101651943?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![8](http://img.blog.csdn.net/20171120101709822?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![9](http://img.blog.csdn.net/20171120101719262?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


![10](http://img.blog.csdn.net/20171120101756945?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![11](http://img.blog.csdn.net/20171120101822847?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


![12](http://img.blog.csdn.net/20171120101832412?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


![13](http://img.blog.csdn.net/20171120101843800?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![14](http://img.blog.csdn.net/20171120101854670?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


![15](http://img.blog.csdn.net/20171120101907654?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)









下面是源码：
```
public class DVDSet {

	String name;
	int state;
	String date;
	
	public DVDSet() {
		// TODO Auto-generated constructor stub
	}
	
	public DVDSet(String name, int state, String date) {
		super();
		this.name = name;
		this.state = state;
		this.date = date;
	}
	@Override
	public String toString() {
		if(state==0) {
			return  name + "\t  " + "已借出" + "\t" + date ;
		}else {
			return  name + "\t  " + "可借" + "\t" + date ;
		}
		
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getState() {
		return state;
	}
	public void setState(int state) {
		this.state = state;
	}
	public String getDate() {
		return date;
	}
	public void setDate(String date) {
		this.date = date;
	}
}

```


```
import java.text.DecimalFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Scanner;

public class DVDMgr {

	final int LENGTH = 20;

	DVDSet[] dvd = new DVDSet[LENGTH];

	Scanner scanner = new Scanner(System.in);

	// 在构造器中传入日期样式
	// SimpleDateFormat sdf=new SimpleDateFormat(
	// "yyyy.MM.dd G 'at' HH:mm:ss z");
	SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");

	public void startMenu() {
		printMain();
		// 初始化
		initial();

		int key = scanner.nextInt();

		// 菜单切换
		switch (key) {
		case 1:
			System.out.print("请输入新增的DVD名称：");

			String dName = scanner.next();
			System.out.println("您要增加的DVD名字是《" + dName + "》");

			add(dName);
			returnMain();
			break;
		case 2:
			System.out.println("DVD信息如下：");
			System.out.println("name\tstate\tdate");
			search();

			returnMain();
			break;
		case 3:
			System.out.print("请输入要删除的DVD名称：");
			String deleteN = scanner.next();
			System.out.println("您要删除的DVD名称为《" + deleteN + "》");

			delete(deleteN);
			returnMain();
			break;
		case 4:
			System.out.print("请输入你要借的DVD的名称：");
			String name = scanner.next();

			loan(name);
			returnMain();
			break;
		case 5:
			System.out.print("请输入你要归还的DVD的名称：");
			String guihuan = scanner.next();

			returnDvd(guihuan);
			returnMain();
			break;
		case 6:
			System.exit(0);
			break;

		default:
			System.out.println("您输入的有误，请重新输入！");
			startMenu();
			break;
		}

	}

	public void printMain() {
		// 主菜单显示
		System.out.println("欢迎使用迷你DVD管理器");
		System.out.println("========================================================");
		System.out.println("1. 新增DVD");
		System.out.println("2. 查看DVD");
		System.out.println("3. 删除DVD");
		System.out.println("4. 借出 DVD");
		System.out.println("5. 归还DVD");
		System.out.println("6. 退       出");
		System.out.println("========================================================");
		System.out.print("请选择：");
	}

	public void returnMain() {
		System.out.print("输入0返回：");
		int reStep = scanner.nextInt();

		if (reStep == 0) {
			startMenu();
		} else {
			System.out.println("您输入的有误，请重新输入!");
			returnMain();
		}
	}

	public void initial() {
		dvd[0] = new DVDSet("罗马假日", 0, "2013-7-1");
		dvd[1] = new DVDSet("风声鹤唳", 1, "");
		dvd[2] = new DVDSet("浪漫满屋", 1, "");
	}

	public void search() {
		for (int i = 0; i < dvd.length; i++) {
			if (dvd[i] != null) {
				System.out.println(dvd[i]);
			} else {
				break;
			}
		}
	}

	public void add(String string) {

		String formatDate = dvdDate();

		for (int i = 0; i < dvd.length; i++) {
			if (dvd[i] == null) {
				dvd[i] = new DVDSet();
				dvd[i].setName(string);
				dvd[i].setState(1);
				dvd[i].setDate(formatDate);
				break;
			}
		}
	}

	public void delete(String string) {
		boolean b = false;
		for (int i = 0; i < dvd.length; i++) {
			if (dvd[i] == null) {
				continue;
			}
			if (string.equals(dvd[i].getName())) {
				System.out.println(dvd[i]);
				if (dvd[i].getState() == 0) {
					System.out.println("你要删除的DVD已借出，不可以删除！");
					break;
				}
				if (i < dvd.length - 1) {
					dvd[i] = dvd[i + 1];
				}
				dvd[dvd.length - 1] = null;
				System.out.println("您删除的《" + string + "》" + "删除成功");
				b = true;
			}
		}
		if (!b) {
			System.out.println("你输入的DVD不存在。");
		}
		// returnMain();
	}

	public void loan(String string) {
		boolean b = false;
		for (int i = 0; i < dvd.length; i++) {
			if (dvd[i] == null) {
				continue;
			}
			if (string.equals(dvd[i].getName())) {
				if (dvd[i].getState() != 0) {
					b = true;
					dvd[i].setState(0);
					System.out.println("你成功借出了《" + string + "》");
				} else {
					System.out.println("此书已经借出");
					break;
				}
			}
		}
		if (!b) {
			System.out.println("你要借的DVD不存在");
		}
	}

	public void returnDvd(String string) {
		boolean b = false;
		String date = dvdDate();
		DecimalFormat df = new DecimalFormat("######0.00");

		for (int i = 0; i < dvd.length; i++) {
			if (dvd[i] == null) {
				continue;
			}
			if (string.equals(dvd[i].getName())) {
				if (dvd[i].getState() != 1) {
					b = true;
					dvd[i].setState(1);
					System.out.println("你成功归还了《" + string + "》");
					System.out.println("借出日期为：" + dvd[i].getDate());
					System.out.println("归还日期为：" + date);

					try {
						long a = sdf.parse(date).getTime() - sdf.parse(dvd[i].getDate()).getTime();
						double d = a * 0.0000000001;

						df.format(d);
						System.out.println("应付租金（元）：" + d);
					} catch (ParseException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}

				} else {
					System.out.println("此书未借出");
					break;
				}
			}
		}
		if (!b) {
			System.out.println("你要归还的DVD不存在");
		}
	}

	public String dvdDate() {
		// sdf=new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
		// 当前系统时间
		Date date = new Date();
		// 调用format(Date date)对象传入的日期参数进行格式化
		// format(Date date)将日期转化成字符串
		String formatDate = sdf.format(date);
		// System.out.println("格式化后的日期为:" + formatDate);

		return formatDate;
	}

}
```

```
public class Client {

	public static void main(String[] args) {
		DVDMgr dvdMgr = new DVDMgr();
		dvdMgr.startMenu();
	}
}

```



[我的csdn博客](https://blog.csdn.net/prairie97)




