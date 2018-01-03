title: STM32 systick时钟
date: 2016/7/15 10:08:23
tags:
- STM32
categories:
- STM32
---

# STM32 systick时钟

## 项目说明
之前使用的延迟函数是利用循环语句占用cpu时间来实现的，这样的延时并不准确。而systick定时器是包含在M3的内核里，捆绑在 NVIC 中的。使用它可以做到很精确的延时。

<!-- more -->

## 知识点
- 1、它是24位倒计数的定时器，当定时器 **进行减1** 计数到0的时候，将从LOAD寄存器中 **自动重装** 定时器初值，如果开启中断的话，同时它还是产生异常中断信号。**默认使用的为系统的72M频率进行8分频得到的9Mhz。**
- 2、systick定时器的时钟来源是来自系统时钟，不过它的时钟可以选择成直接取自系统时钟，还可以将系统时钟8分频之后再赋给systick定时器。但是该定时器的时钟频率可随系统时钟频率的变化而改变。而STM32这个芯片本身就可以设定不同的晶振源并设置不同的系统频率，所以该定时器的工作频率是随系统频率而变的。
- 3、它的功能由4个寄存器来决定。其中，寄存器LOAD负责记录初值，寄存器VAL负责记录当前值。 这两个寄存器均为24位寄存器，所以其 **大计数值为0xffffff** 。因此，在使用默认的9Mhz频率时，**延迟时间理论上最大可到(0xffffff/9000)秒，即1.864秒。** 因此在进行延迟时，一般只延迟1秒，要延迟2秒则调用两次延迟函数就好。


## 部分代码
- 1、延迟微秒函数，延迟毫秒为乘以9000.

```c
void delay_us(u32 i)
{
	u32 temp;
	SysTick->LOAD=9*i;		 //在9Mhz的频率下，意味着每为妙执行9次，因此需要乘以9
	SysTick->CTRL=0X01;		 //控制寄存器最低位为打开systick
	SysTick->VAL=0;		   	 //当前值清零
	do
	{
		temp=SysTick->CTRL;		   
	}
	while((temp&0x01)&&(!(temp&(1<<16))));	 //判断是否计数完成
	SysTick->CTRL=0;	//关闭systick
	SysTick->VAL=0;		//当前计数值清零
}
```

- 2、自定义时钟频率

```c
void RCC_HSE_Configuration() //自定义系统时间（可以修改时钟）
{
	RCC_DeInit(); //将外设RCC寄存器重设为缺省值
	RCC_HSEConfig(RCC_HSE_ON);//设置外部高速晶振（HSE）
	if(RCC_WaitForHSEStartUp()==SUCCESS) //等待HSE起振
	{
		RCC_HCLKConfig(RCC_SYSCLK_Div1);//设置AHB时钟（HCLK）
		RCC_PCLK1Config(RCC_HCLK_Div2);//设置低速AHB时钟（PCLK1）
		RCC_PCLK2Config(RCC_HCLK_Div1);//设置高速AHB时钟（PCLK2）
		RCC_PLLConfig(RCC_PLLSource_HSE_Div2,RCC_PLLMul_9);//设置PLL时钟源及倍频系数
		RCC_PLLCmd(ENABLE); //使能或者失能PLL
		while(RCC_GetFlagStatus(RCC_FLAG_PLLRDY)==RESET);//检查指定的RCC标志位设置与否,PLL就绪
		RCC_SYSCLKConfig(RCC_SYSCLKSource_PLLCLK);//设置系统时钟（SYSCLK）
		while(RCC_GetSYSCLKSource()!=0x08);//返回用作系统时钟的时钟源,0x08：PLL作为系统时钟
	}
}
```

## 2017/11/19 更新
- 1、在较新的库函数版本中，systick用于延时的时候是非常实用的，而且配置上也比较简单。都是根据系统主频的配置自动设置初始计数值，将定时器的定时时间设置成1ms。
- 2、1ms产生一次中断，在中断函数中执行计数值`uwTick`加1操作。
- 3、延迟函数中通过对`uwTick`的数值进行判断来实现。这样在延迟时也可以比较灵活，对参数i（单位为ms）传值便可以配置延迟时间。代码如下：
	```c
	__weak void HAL_Delay(__IO uint32_t Delay)
	{
	  uint32_t tickstart = 0U;
	  tickstart = HAL_GetTick();
	  while((HAL_GetTick() - tickstart) < Delay)
	  {
	  }
	}
	```
