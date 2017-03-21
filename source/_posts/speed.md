title: speed
date: 2016/6/10 10:08:23
tags:
- 编程
- java
categories:
- 编程
---


**题目：** 一辆车现在的里程表是一个回文数，为95859，两个小时后是一个新的回文数。求这个回文数是多少？车速是多少？

<!-- more -->

**java代码：**
```java
package speed;

public class speed {

    public static void main(String[] args) {
        for(int i=95860;i<96200;i++) {           
            if (isPalindrome(i)) {
             System.out.println("mileage = "+i);
             System.out.println("speed = "+(i-95859)/2);
            }
        }

    }

    //判断一个数是否是回文数
    public static boolean isPalindrome(int i) {
        int a = i/10;
        int b = i%10;
        int count = 10000;
        int c = b*count;

        while (a>0) {
            count=count/10;
            b = a%10;
            c += b*count;          
            if (c==i) {
            //    System.out.println(c);
                return true;
            }
            a = a/10;
        }      
        return false;
    }
}

```
- 1、这题主要是需要找到大于95859的回文数，而同时应该考虑到汽车的车速不可能突破200。因此，只需在（95860，95859+2x200）这个区间去寻找回文数就好。
- 2、代码中有一段程序是专门用来判断一个数是否是回文数的，返回一个布尔值。判断一个数是否是回文数的方法主要还是采用对一个数做除法和取余操作，循环操作后就可以识别出一个多位数的每一位分别是什么。
- 3、先得出的结果是该数的个位，而个位应该作为新数的头一位，方法是乘以10000，第二个取出的数乘以1000，依次类比。这样才能去和原数字比较是否相等，若相等则证明该数字就是回文数。
- 4、主函数得到一个true类型的返回值时，即可显示该里程数，该里程数减去原里程数95859再除以2即为速度值。
