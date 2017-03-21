title: STM32 窗口看门狗实验
date: 2016/7/21 10:08:23
tags:
- STM32
- 看门狗
categories:
- STM32
---
# STM32 窗口看门狗实验

## 项目说明
STM32有两个看门狗，一个为独立看门狗，有自己的时钟；另一个为窗口看门狗，没有独立的时钟。另外，两种看门狗还有不同的喂狗方式。独立看门狗为向特定的寄存器写0XAAAA指令，窗口看门狗为利用产生的中断来喂狗。

<!-- more -->

## 知识点
- 1、窗口看门狗的特点在于可以设置一个窗口，这个窗口的上限值可以自己设置，是一个7位数值，下限为固定值0X40，当计数下降到窗口上限时会产生中断，在中断中可以对计数值复位，从而避免看门狗复位。
- 2、窗口看门狗有三个寄存器，分别问CR，低八位有效，其中低7位用于设定减一计数器的初值。另外第八位为启动位；CFR，低10位有效，低7位用于设置窗口上限，后两位用于设置时基，第十位用于开启提前唤醒中断使能（即当计数值小于窗口上限时，产生中断，在中断中进行看门狗的计数复位）；还有SR，仅最低位有效，为标志位，用来记录当前是否有提前唤醒的标志，用软件清零。当计数器值达到40h时，此位由硬件置1。它必须通过软件写0来清除。对此位写1无效。即使中断未被使能，在计数器的值达到0X40的时候，此位也会被置1。
- 3、编写窗口看门狗的中断服务函数，通过该函数来喂狗，喂狗要快，否则当窗口看门狗计数器值减到0X3F的时候，就会引起软复位了。在中断服务函数里面也要将状态寄存器的EWIF位清空。

## 部分代码
- 1、由于窗口看门狗没有独立的时钟，所以需要为其设置时钟。下面为窗口看门狗初始化函数。
```c
void wwdg_init()
{
	NVIC_InitTypeDef NVIC_InitStructure;		//中断结构体定义
	RCC_APB1PeriphClockCmd(RCC_APB1Periph_WWDG, ENABLE); // WWDG时钟使能

	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
	NVIC_InitStructure.NVIC_IRQChannel = WWDG_IRQn;
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0;
	NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0;//看门狗的优先级要高于其他
	NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
	NVIC_Init(&NVIC_InitStructure);

	WWDG_SetPrescaler(WWDG_Prescaler_8);//设置WWDG预分频数值
   	WWDG_SetWindowValue(0x5F);//窗口上边界数值
   	WWDG_Enable(0x7F);//使能窗口看门狗
   	WWDG_ClearFlag(); //清除提前唤醒中断标志
   	WWDG_EnableIT();//开启窗口看门狗中断
}
```

- 2、在中断函数中，需要对看门狗的计数进行复位。
```c
void WWDG_IRQHandler(void)	 //窗口看门狗中断程序
{
   WWDG_SetCounter(0x7F);	//喂狗
   WWDG_ClearFlag();
   printf("喂狗\r\n");
}
```

- 3、在主函数中，首先输出这三条函数，之后就会进入中断函数输出喂狗。
```c
int main()
{
	printf_init();	 //printf初始化
	wwdg_init();  //窗口看门狗初始化配置
	printf("cyang.tech！\r\n");
	printf("窗口看门狗\r\n");
	while(1)
	{
		printf("看门狗函数\r\n");
		delay_ms(1000);
	}
}
```
