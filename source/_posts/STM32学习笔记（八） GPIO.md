title: STM32学习笔记（八） GPIO
tags:
- STM32
categories:
- STM32
---

- 1、STM32的每个IO端口都有7个寄存器来控制。分别为：

功能 | 名称 | 位数
---|---|---
配置寄存器1 | CRL |32
配置寄存器2 | CRH |32
数据寄存器1 | IDR |32
数据寄存器2 | ODR |32
置位复位寄存器 | BSRR |32
复位寄存器| BRR | 16
锁定寄存器| LCRR |32

<!-- more -->

- 2、常用的有四个，分别为CRL,CRH,IDR,ODR。其中CRL.CRH控制着每个IO口的模式和输出速率,输出速率有10M,2M,50M。
- 3、每个IO口都可以被软件配置成8种模式，分别为如下8种。

输入 | 输出
---|---
输入浮空 | 开漏输出
输入上拉 | 推挽输出
输入下拉 | 推挽式复用功能
模拟输入 | 开漏复用功能

- 4、GPIO_Init函数

（1）该函数用于初始化IO口。

（2）具有两个输入参数，分别为GPIOx,&GPIO_InitStruct.

（3）返回值为空。

（4）示例`GPIO_Init(GPIOB,&GPIO_InitStruct)`

- 5、GPIO_InitStruct 定义在 `stm32f10x_gpio.h` 文件中。代码如下：

```
typedef struct{
uint16_t GPIO_Pin;
GPIOSpeed_TypeDef GPIO_Speed;
GPIOMode_TypeDef GPIO_Mode;
} GPIO_InitTypeDef;

```
- 6、GPIO口所用的时钟PCLK2采用默认值，为72MHz。采用默认值不可以修改分频器，但是外设时钟默认处在关闭状态，所以外设时钟一般会在初始化外设时钟的时候开启，开启和关闭外设时钟也有封装好的库函数`RCC_APB2PeriphClockCmd()`。

（1）使用示例：`RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);`

- 7、控制IO口的输出电平是通过两个函数来控制的。

（1）输出高电平示例`GPIO_SetBits(GPIOA,GPIO_Pin_10|GPIO_Pin_15);`

（2）输出低电平示例`GPIO_ResetBits(GPIOA,GPIO_Pin_10|GPIO_Pin_15);`
