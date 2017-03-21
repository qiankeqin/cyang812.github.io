title: STM32 printf函数
date: 2016/7/13 10:08:23
tags:
- STM32
categories:
- STM32
---

# STM32 printf函数

## 项目说明
STM32有5路串口，所以可以很容易的与PC机进行通信。而通过C语言的printf是一种很容易理解的方式。使用232芯片与PC进行通信，定时向通过串口发送数据到PC机进行显示。

<!-- more -->

## 知识点
- 1、要实现串口发送printf的功能，首先需要重新编写printf函数，对其进行重定向。这个函数的功能就是输入一个参数，然后通过串口发送这个参数。
- 2、改变了printf的功能，所以需要进行重定向。这会使的在运行到printf函数时调用的是自定义的printf函数。
- 3、其余的初始化函数是和232初始化一样的，因为事实上就是通过232芯片来实现的发送数据。

## 部分代码
- 1、自定义的printf函数
```c
int fputc(int ch,FILE *p)  //函数默认的，在使用printf时自动调用。
{
	USART_SendData(USART1,(u8)ch);
	while(USART_GetFlagStatus(USART1,USART_FLAG_TXE)==RESET);
	return ch;
}
```
