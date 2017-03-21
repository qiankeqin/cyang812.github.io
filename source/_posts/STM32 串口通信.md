title: STM32 串口通信
date: 2016/7/13 10:08:23
tags:
- STM32
categories:
- STM32
---

# STM32 串口通信

## 项目说明
使用STM32的串口与PC进行通信，使用的芯片为232或者485。从电脑端发送一和数值，STM32接收完成后通过中断函数进行加1操作，之后再通过串口发送到电脑进行显示。

<!-- more -->

## 知识点
- 1、串口操作的一般步骤。

（1) 打开 GPIO 的时钟使能和 USART 的时钟使能。

（2) 设置串口 IO 的 IO 口模式。 （一般输入是模拟输入， 输出是复用推挽输出）

（3) 初始化 USART。 （包括设置波特率、数据长度、停止位、效验位等）

（4) 如果使用中断接收的话，那么还要设置 NVIC 并打开中断使能。（即设置 设置它的中断优先级。 ）

- 2、串口中断函数的操作步骤

（1）发送标志位清零。这一步主要是用于清空下面会执行到的发送数据的标志位。
（2）判断中断是否发生
（3）获取接收的数据并且加1后进行发送。
（4）判断是否发送完成，若完成就退中断。

- 3、232芯片和485芯片都可以用作与PC进行通信，两者的代码差不多，但是232芯片为全双工通信，而485芯片为半双工通信。因此，485芯片比232芯片会多一个控制发送还是接收的使能管脚，需要通过该管脚进行置0或置1进行发送或者接收的切换。

## 部分代码

- 1、设置USART
```c
USART_InitStructure.USART_BaudRate=9600;   //波特率设置为9600	//波特率
USART_InitStructure.USART_WordLength=USART_WordLength_8b;		//数据长8位
USART_InitStructure.USART_StopBits=USART_StopBits_1;			//1位停止位
USART_InitStructure.USART_Parity=USART_Parity_No;				//无效验
USART_InitStructure.USART_HardwareFlowControl=USART_HardwareFlowControl_None; //失能硬件流
USART_InitStructure.USART_Mode=USART_Mode_Rx|USART_Mode_Tx;	 //开启发送和接受模式
USART_Init(USART1,&USART_InitStructure);	/* 初始化USART1 */
USART_Cmd(USART1, ENABLE);		   /* 使能USART1 */
USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);//使能或者失能指定的USART中断 接收中断
USART_ClearFlag(USART1,USART_FLAG_TC);//清除USARTx的待处理标志位

```

- 2、设置NVIC
```c
/* 设置NVIC参数 */
NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn; 	   //打开USART1的全局中断
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0; 	 //抢占优先级为0
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0; 			//响应优先级为0
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE; 			 //使能
NVIC_Init(&NVIC_InitStructure);

```

- 3、中断函数
```c
void USART1_IRQHandler(void)	//串口1中断函数
{
	static u8 k;
	USART_ClearFlag(USART1,USART_FLAG_TC);
	if(USART_GetITStatus(USART1,USART_IT_RXNE)!=Bit_RESET)//检查指定的USART中断发生与否
	{
		k=USART_ReceiveData(USART1);
		k++;
		USART_SendData(USART1,k);//通过外设USARTx发送单个数据
		while(USART_GetFlagStatus(USART1,USART_FLAG_TXE)==Bit_RESET);	//判断是否发送完成
	}
}
```
