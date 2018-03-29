title: STM32F469 CoreMark跑分测试
date: 2018/2/27 19:52:23
tags:
- 编程
- IAR
- STM32
- CoreMark
categories:
- 嵌入式
---

# 一、CoreMark 介绍
CoreMark 是一项测试处理器性能的基准测试。代码使用 C 语言写出，包含：列表，数学矩阵操作和状态及 CRC 等运算法则。目前 CoreMark 已迅速成为测量与比较处理器性能的业界基准测试。CoreMark 的得分越高，意味着性能更高。

<!-- more -->

# 二、代码移植
移植 CoreMark 的测试代码到 STM32 平台非常简单。ST 官方资料文档就有移植步骤的详细说明，[文档地址](http://www.stmcu.org/document/detail/index/id-217064)。

文档中使用 STM32Cube MX 生成测试工程。由于本次测试平台为 STM32F469I-DISCOVERY，可直接在固件库\en.stm32cubef4\STM32Cube_FW_F4_V1.14.0\Projects\STM32469I-Discovery\Templates的基础上移植代码。

移植代码需要改动的地方在 core_portme.c 中。修改方法在文档中均有详细说明。

在 IAR 环境下，为了正确编译代码，可能需要添加部分函数的函数原型，当然，也可以修改编译选项为不需要函数原型。同时应合理设置栈大小，可通过单步调试查看栈是否溢出，若溢出，IAR 状态栏会有警告。

# 三、测试结果
主频为 180 Mhz 时，测试几种不同的迭代次数，CoreMark 最高跑分为636，最低跑分为612。
官方给出的测试结果为608。
串口结果如下图：

![这里写图片描述](http://img.blog.csdn.net/20180227195105586?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

主频 120Mhz 时，跑分为 411， 主频为 60Mhz 时，跑分为 205。