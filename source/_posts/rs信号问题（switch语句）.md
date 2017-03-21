title: 信号问题（switch语句）
date: 2016/4/19 10:08:23
tags:
- 编程
- java
categories:
- 编程
---

**题目内容：**

无线电台的RS制信号报告是由三两个部分组成的：

R(Readability) 信号可辨度即清晰度.

S(Strength)    信号强度即大小.

<!-- more -->

其中R位于报告第一位，共分5级，用1—5数字表示.

1---Unreadable

2---Barely readable, occasional words distinguishable

3---Readable with considerable difficulty

4---Readable with practically no difficulty

5---Perfectly readable

报告第二位是S，共分九个级别，用1—9中的一位数字表示

1---Faint signals, barely perceptible

2---Very weak signals

3---Weak signals

4---Fair signals

5---Fairly good signals

6---Good signals

7---Moderately strong signals

8---Strong signals

9---Extremely strong signals

现在，你的程序要读入一个信号报告的数字，然后输出对应的含义。如读到59，则输出：

Extremely strong signals, perfectly readable.

**代码：**
```java
  package rS_explain;

  import java.util.Scanner;

  public class Main {

  	public static void main(String[] args) {
  		int a,b=0;
  		String o1="";
  		String o2="";
  		Scanner in = new Scanner(System.in);
  		int s = in.nextInt();
  		a = s/10;
  		b = s-a*10;

  		if (s<11|a>59)
  		{
  			System.out.println("input error");
  		}
  		switch (a)
  		{
  		case 1: o1="unreadable.";break;
  		case 2: o1="barely readable,occasional words distingushable.";break;
  		case 3: o1="readable with considerable difficulty.";break;
  		case 4: o1="readable with practically no difficulty.";break;
  		case 5: o1="perfect with practically no difficulty.";break;
  		}
  		switch (b)
  		{
  		case 1: o2="Faint signals,barely perceptible, ";break;
  		case 2: o2="Very weal signals, ";break;
  		case 3: o2="Weak signals, ";break;
  		case 4: o2="Fair good signals, ";break;
  		case 5: o2="Fairly good signals, ";break;
  		case 6: o2="Good signals, ";break;
  		case 7: o2="Moderately strong signals, ";break;
  		case 8: o2="Strong signals, ";break;
  		case 9: o2="Extermely strong signals, ";break;
  		}
  	System.out.print(o2);
  	System.out.print(o1);
  	}
  }
```
- 1、这题主要的算法是解释数字所代表的字符串信息。使用了switch case语句用于选择。
- 2、字符串（String）在java里属于一种类，不算在基本的数据类型里，而在引用类型里。在本题里，使用的方法和基本数据类型一样，先定义两个字符串o1,o2。后面就可以直接赋值了。
- 3、字符串的赋值时，要是有双引号"".
