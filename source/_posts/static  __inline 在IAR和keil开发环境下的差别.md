title: static  __inline 在IAR和keil开发环境下的差别
date: 2016/11/3 11:19:23
tags:
- STM32
- keil
- IAR
- C语言
categories:
- 硬件
---

static  __inline这条语句在IAR和Keil下的需要写成不同的形式，否则会报错。

<!-- more -->

如下：
1、IAR错误
![这里写图片描述](http://img.blog.csdn.net/20161029185507754)
2、IAR正确
![这里写图片描述](http://img.blog.csdn.net/20161029185541584)
3、Keil错误
![这里写图片描述](http://img.blog.csdn.net/20161029185550240)
4、Keil正确
![这里写图片描述](http://img.blog.csdn.net/20161029185556693)

总结就是，在IAR环境下，需要写成`static inline`，而在keil环境下，需要写成`static __inline`
