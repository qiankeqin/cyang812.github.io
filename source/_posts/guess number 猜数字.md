title: guess number 猜数字
date: 2016/8/22 10:08:23
tags:
- 编程
- python
- Pycharm
categories:
- 编程
---

## 一、说明
使用python编写的猜数字游戏

<!-- more -->

## 二、python代码

```python
lucky_number = 12
count = 6

while (count>0):

    input_number = (int)(input("please input a number from 0 t 100:"))
    count = count - 1

    if input_number<lucky_number:
        print("the lucky number is bigger")
    elif input_number>lucky_number:
        print("the lucke number is smaller")
    else:
        print("bingo")
        break

else:
    print("you have no chance")
```
