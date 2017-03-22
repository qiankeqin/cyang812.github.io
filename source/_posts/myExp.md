title: myExp
date: 2016/5/21 10:08:23
tags:
- 编程
- java
categories:
- 编程
---

**题目：** 计算exp的一种方法是`exp(x)=1+x+x^2/2!+x^3/3!+……`

**要求：** 编写一个myExp方法，计算该公式。

<!-- more -->

**java代码：**

```java
package myExp;

import java.util.Scanner;

public class myExp {

    public static void main(String[] args) {
        System.out.println("please enter a number");
        Scanner in = new Scanner(System.in);
        int x = in.nextInt();
        System.out.println("myExp: \t "+myExp(x));
        System.out.println("Math.exp:"+Math.exp(x));
    }

    //计算阶乘
    public static long factorial(int n) {
        long sum = 1;
        while(n>1) {
            sum=sum*n;
            n--;
        }
        return sum;
    }

    //计算x的n次方
    public static long myPow(int x, int n) {
        long sum = 1;
        while (n>0) {
            sum=sum*x;
            n--;    
        }
         return sum;   
    }

    //计算exp
    public static double myExp(int x){
        double result = 1.0;
        double z = 0;
        for(int i=1;i<10;i++) {
            long pow = myPow(x,i);
            long fac = factorial(i);
            z =(double) pow/fac;
            result = result+z;
            //System.out.println(i+"   "+result);
        }
        return result;
    }
}

```

**输出示例：**
```
please enter a number
1
myExp: 	 2.7182815255731922
Math.exp:2.718281828459045

```

- 1、这个程序中有三个函数，分别为计算阶乘，计算pow，计算exp。计算exp需要用到阶乘和pow。需要注意的是，计算阶乘时，数字增长很快。超过数据类型的限制时，计算结果就会不正确，此时数据变为0，所以出现异常。`java.lang.ArithmeticException:/by zero`.即出现除以0的情况，因此程序抛出异常。
- 2、同时，由于阶乘以及pow方法的返回值类型都是整数，而如果两者的商小于1时，整个结果就不会有变化，此时需要使用强制类型转换`(double)`.
- 3、事实上，由于阶乘数值过大，决定了n值不能取太大，而此时计算误差就会比较大。
