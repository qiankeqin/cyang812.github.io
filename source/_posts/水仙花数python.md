title: 水仙花数python
date: 2016/8/16 10:08:23
tags:
- 编程
- python
- Pycharm
categories:
- 编程
---

## 一、说明
 水仙花数是指一个 n 位数 ( n≥3 )，它的每个位上的数字的 n 次幂之和等于它本身。（例如：1^3 + 5^3+ 3^3 = 153）。同理，若为四阶的数，是四次方。在编程时要注意这一点。

<!-- more -->

## 二、python代码
- 1、这和之前的[java写的代码](http://cyang.tech/2016/04/28/%E6%B0%B4%E4%BB%99%E8%8A%B1%E6%95%B0/)，是另一种不同的算法。
- 2、在pyhton3中的除法默认不是int类型而是float,所以要强制转换一下。


```python
for i in range(152,1000):
    x = int(i/100)
    y = int((i/10)%10)
    z = int(i%10)
    ans = (x**3)+(y**3)+(z**3)
    if i == ans:
        print(i)

```
