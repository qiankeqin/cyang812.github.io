title: STM32 待机实验 
date: 2016/7/16 10:08:23   
tags:
- STM32
categories:
- STM32
---

# STM32 待机实验    

## 项目说明
在STM32中，存在三种低功耗模式。分别为睡眠模式，停机模式，待机模式。这三种低功耗模式对应的系统状态不一样，唤醒方式不一样。其中，待机模式是三者中功耗最低的模式。进入低功耗模式的方法也很简单，系统库函数中就存在。

<!-- more -->

## 知识点
- 1、有三种方式进入待机模式。分别为设置M3系统控制寄存器中的SLEEPDEEP位；设置电源控制寄存器(PWR_CR)中的PDDS位；清除电源控制/状态寄存器（PWR_CSR）中的WUF位。
- 2、有四种方式退出待机模式，分别为WKUP引脚的上升沿；RTC闹钟事件的上升沿；NRST引脚上外部复位；IWDG复位。
- 3、进入待机模式可以直接调用系统函数，不过在进入前需要先设置时钟，进入方式，退出方式。在这里，我们设置进入方式为方式1，唤醒方式为方式1.

## 部分代码
- 1、首先需要待机模式进行设置。

```c
void standmodeinit()   //待机模式
{
	NVIC_SystemLPConfig(NVIC_LP_SLEEPDEEP,ENABLE);//选择系统进入低功耗模式的条件
	RCC_APB1PeriphClockCmd(RCC_APB1Periph_PWR,ENABLE);//使能PWR外设时钟
	PWR_WakeUpPinCmd(ENABLE);//使能唤醒管脚	使能或者失能唤醒管脚功能
	PWR_EnterSTANDBYMode();//进入待机模式		
}
```

- 2、在主函数中，通过5秒的延迟后进入待机模式，之后使用K_UP按键退出待机模式，再过5秒再次进入。因为K-UP按键就是连接的PA0，而PA0就是WKUP功能。

```c
int main()
{
	printf_init();	  //printf初始化
	while(1)
	{
		printf("time: 5\r\n");
		delay_ms(1000);	//隔1秒显示计数
		printf("time: 4\r\n");
		delay_ms(1000);
		printf("time: 3\r\n");
		delay_ms(1000);
		printf("time: 2\r\n");
		delay_ms(1000);
		printf("time: 1\r\n");
		delay_ms(1000);
		printf("进入系统待机模式\r\n");
		standmodeinit();//待机模式配置
	}			
}
```
