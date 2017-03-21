title: STM32 外部中断实验
date: 2016/7/14 19:34:23
tags:
- STM32
categories:
- STM32
---

# STM32 外部中断实验

## 项目说明
STM32共用68个外部中断，16个内部中断，并且有16级的中断优先级设置。比起51内核单片机，STM32的中断设置要复杂和强大的多。

<!-- more -->

## 知识点
- 1、68个外部中断源包括很多种类，比如iic中断，uart中断，can中断等。
- 2、**两种中断优先级**。每一个中断源都要设置中断优先等级，由4位数来控制，所以共有16种。这4位数字中包括两种优先级，分别为 **抢占式优先级和响应式优先级。抢占式优先级的优先级高于响应式优先级，同时，抢占式优先级支持嵌套，响应式优先级不支持嵌套。**
- 3、4位数分两种优先级，共有 **5种搭配方式**，即分别为抢占式优先级和响应式优先级的位数分别为4/0，3/1，2/2，1/3，0/4. 采用库函数编写代码是，分别为NVIC_PriorityGroup_1，NVIC_PriorityGroup_2 ... NVIC_PriorityGroup_5 。
- 4、这里采用的是外部按键中断，其本质还是GPIO口的输入中断，对应中断源为 **EXTI中断** 。STM32的每一个GPIO口均可以作为中断输入，但是由于EXTI中断的特性，STM32它 **一次性同时使用的外部 中断只能有 16 个， 而且同时做外部中断的 IO 口序号也是不能相同的**， 比如， 你使用 PA0 做外部中断了，那么就不能同时使用 PB0 做外部中断了。
- 5、配置GPIO口中断时，使用下降沿触发，那么就选择上拉输入模式，要使用上升沿触发，那么就选择下拉输入模式。


## 部分代码
- 1、外部中断函数初始化。这里需要设置GPIO，之后选择外部中断模式，最后进行参数配置。

```c
void exti_init()  //外部中断初始化
{
	GPIO_InitTypeDef GPIO_InitStructure;

	EXTI_InitTypeDef EXTI_InitStructure;

	NVIC_InitTypeDef NVIC_InitStructure;

	/* 开启GPIO时钟 */
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO,ENABLE);
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOE,ENABLE);

	GPIO_InitStructure.GPIO_Pin=k_left;
	GPIO_InitStructure.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_InitStructure.GPIO_Speed=GPIO_Speed_50MHz;
	GPIO_Init(GPIOE,&GPIO_InitStructure);

	GPIO_EXTILineConfig(GPIO_PortSourceGPIOE, GPIO_PinSource2);//选择GPIO管脚用作外部中断线路
	//此处一定要记住给端口管脚加上中断外部线路
	/* 设置外部中断的模式 */
	EXTI_InitStructure.EXTI_Line=EXTI_Line2;
	EXTI_InitStructure.EXTI_Mode=EXTI_Mode_Interrupt;
	EXTI_InitStructure.EXTI_Trigger=EXTI_Trigger_Falling;
	EXTI_InitStructure.EXTI_LineCmd = ENABLE;
	EXTI_Init(&EXTI_InitStructure);

	/* 设置NVIC参数 */
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);		 
	NVIC_InitStructure.NVIC_IRQChannel = EXTI2_IRQn; 	//打开EXTI2的全局中断
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0; //抢占优先级为0
	NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0;		  //响应优先级为0
	NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE; 		  //使能
	NVIC_Init(&NVIC_InitStructure); 		
}

```

- 2、STM32的中断和之前写的51不一样。**中断后的算法逻辑需要添加到user文件夹下的stm32f10x_it.c文件中。** 如下为实现按键控制LED状态，代码为stm32f10x_it.c代码中的部分。

```c
void EXTI2_IRQHandler()	   //外部中断2中断函数
{
	if(EXTI_GetITStatus(EXTI_Line2)==SET)
	{
   		EXTI_ClearITPendingBit(EXTI_Line0);//清除EXTI线路挂起位
		delay_ms(10);//消抖处理
		if(GPIO_ReadInputDataBit(GPIOE,GPIO_Pin_2)==Bit_RESET)	   //k_left按键按下
		{
			delay_ms(10);//消抖处理
			if(GPIO_ReadOutputDataBit(GPIOC,GPIO_Pin_0)==Bit_RESET)
			{
				//LED 熄灭
			   GPIO_SetBits(GPIOC,GPIO_Pin_0);
			}
			else
			{
			   //LED 发光
				GPIO_ResetBits(GPIOC,GPIO_Pin_0);
			}
		}
		while(GPIO_ReadInputDataBit(GPIOE,GPIO_Pin_2)==0);
	}		
}
```
