title: IAR开发STM32堆栈设置
date: 2018-10-25 22:31:19
tags:
- STACK
- 堆栈
- IAR
categories:
- 嵌入式
---

# 一、前言
关于堆栈的定义在此就不赘述，详细内容可以看[这篇博客](https://blog.csdn.net/u011303443/article/details/78989683)。
堆栈溢出会导致野指针，返回地址错误等问题，通常程序已经无法正常运行，进入 HardFault 异常中断。为了避免这种情况，一般会分配较大的空间用做栈，可是如果仅仅为了安全就分配大空间的栈势必导致内存浪费。本文介绍两种获取栈最大消耗的方法，以方便合理设置栈的大小。

<!-- more -->

# 二、方法
## 1、方法一
栈指针 SP 指向的位置可以反应出当前栈的消耗量。在 STM32 中，栈是向下生长的，如果我们定期的获取栈指针 SP 的值，比较后得到一个最小值，就代表了栈的最大消耗量。而如何才能定期去获取栈指针 SP 的值呢？可以使用定时器产生一个周期性的中断，在中段函数中获取栈指针 SP 的值。最简单的方法就是在系统滴答定时器（SysTick）的中断函数中调用栈分析函数。具体可以参看如下的函数。在程序运行结束后，再去获取最大栈消耗量。
```c
static uint32_t max_stack_usage = 0xffffffff;
void stack_parse()
{
	int a = 0;

	if((uint32_t)&a < max_stack_usage)
	{
		max_stack_usage = (uint32_t)&a;
	}
}

uint32_t get_max_stack_usage()
{
	return max_stack_usage;
}
```

由于这个函数是周期执行的，必然对程序的运行性能产生影响，不过这只是为了分析，最终是要移除的。另外由于是周期执行，所以可能会错过一些周期性的压栈，以至于获取的数值并不是最大值。不过，这种方法还是有它的参考意义的。

## 2、方法二
在 IAR 中，可以开启栈使用分析(IAR Embedded Workbench Stack Usage Analysis)，让 IDE 在编译链接阶段就推算出这个程序的栈最大使用量。不过这种方法无法分析使用函数指针的方式调用的函数，也不能确定递归函数的嵌套次数，因此这两种情况下需要使用配置文件来指出这种调用的压栈空间，比较麻烦，具体可看[官方手册](https://www.iar.com/support/resources/articles/detecting-and-avoiding-stack-overflow-in-embedded-systems/)。不过函数指针和递归函数毕竟是少数情况，大多数的函数都是显示调用的。因此 IDE 会分析出一条最长的调用路径，从而分析出最大的栈使用量。步骤如下：

> 1、开启 options > linker >Advanced > Enable stack usage analysis
> 2、编译后查看 map 文件中的 STACK USAGE 部分

内容类似于：
```
*******************************************************************************
*** STACK USAGE
***

  Call Graph Root Category  Max Use  Total Use
  ------------------------  -------  ---------
  Program entry              8 600      8 600
  Uncalled function            256      1 332
```

 # 三、总结
 栈空间用来存放局部变量，部分函数参数，返回地址，以及保存函数调用时主调函数的寄存器内容等。为了减少栈的分配，一定要注意不要在函数中放置很大的局部数组。上文所需的 8600 字节的栈空间，就是因为程序中有一个函数中分配了一个 8192 字节的数组，如下。
 ```c
 int decode_subframe_lpc(FLACContext *s, int32_t* decoded, int pred_order)
{
    int sum, i, j;
    int64_t wsum;
    int coeff_prec, qlevel;
    int coeffs[2048]; //8k, use heap to save stack
    int32_t* output;
	int32_t* reader;
	int* pcoeffs;
	...
}
 ```
可以使用两种方式来修改。其一，将 coeffs 这个数据变成 static 局部静态变量，这样做可以将这个变量从栈中移到 .bss 区域中，不过这种方式并不灵活，相当于 8192 B 的空间被占用，而其他函数无法使用。所以本质上和放在栈空间中区别不大。其二是通过 malloc 的方式灵活申请和释放内存，当不在需要这部分空间时，可以将其释放。
