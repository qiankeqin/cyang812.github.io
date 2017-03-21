title: ZigBee学习笔记（六） CC2530 时钟 电源管理
tags:
- ZigBee
- CC2530
categories:
- 硬件
---

# 一、知识点
- 1、CC2530有四个振荡器，分别为16MHz内部RC振荡器、32KHz内部RC振荡器，32MHz外部晶振和32khz外部晶振。
- 2、系统时钟可以同寄存器来选择使用32MHz的外部晶振或者16MHz的内部RC振荡器。但是当使用RF收发器时，系统时钟必须选择高速并且稳定的32MHz外部晶振。

<!-- more -->

- 3、系统时钟的设置寄存器有时钟控制命令寄存器CLKCONCMD和时钟控制状态寄存器CLKCONSTA。

---

- 4、CC2530提供有5种供电模式，不同的供电模式选择的系统时钟源不同。
- 5、五种模式分别为：

（1）主动模式：又称完全功能模式。在此模式下CPU，外设和RF收发器都是活动的。

（2）空闲模式：除了CPU停止运行之外，其他的运行方式和主动模式相同。

（3）PM1：稳压器的数字内核开启，高频振荡器都不允许。

（4）PM2：具有较低的功耗，稳压器的数字内核关闭，高频振荡器都不允许。

（5）PM3：低功耗模式，稳压器的数字内核关闭，高频振荡器和低频振荡器都不允许。在此模式下，复位和外部I/O端口中断是该模式下仅有的运行功能。

# 二、代码

- 1、在使用串口，DMA，RF等功能时需要对系统时钟进行初始化，以系统时钟选择32MHz晶振为例来设置系统时钟。常见的设置步骤如下：

（1）系统时钟选择32Mhz晶振。

（2）等待32Mhz晶振稳定，因为在设备刚上电时，32MHz晶振启动时间比较长，所以刚上电时，系统运行的是16Mhz的内部RC振荡器，等待32Mhz晶振稳定之后再使用32MHz晶振。

（3）设置定时器时钟输出分频，也可以设置为不分频。

（4）关闭不用的RC振荡器。

示例代码：

```
void InitClock(void) //初始化时钟
{
  unsigned int i;

  //turn on 16MHz RC and 32MHz XOSC
  SLEEPCMD &= ~0x04;
  //wait for 32MHz XOSC stable
  while(!(SLEEPSTA & 0x40));
  //chip bug workaround
  asm("nop");
  //延时63us
  for(i = 0;i < 504;i++)
  {
    asm("nop");
  }
  //Select 32MHz XOSC and the source for 32K clock
  CLKCONCMD = 0x00;
  //Wait for the change to be effective
  while(CLKCONSTA != 0x00);
  //turn off 16MHz RC
  SLEEPCMD = 0x80;
}
```
