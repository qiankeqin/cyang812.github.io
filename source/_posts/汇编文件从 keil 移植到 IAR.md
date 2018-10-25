title: 汇编文件从 keil 移植到 IAR
date: 2018-10-19 19:37:19
tags:
- ASM
- keil
- IAR
categories:
- 嵌入式
---

# 一、前言
汇编文件移植性比较差，不同的内核架构支持的指令集都不一样，就算是相同的内核，在不同的 IDE 下的写法也有可能不一样。同样的文件在 KEIL 下可以正常运行，在 IAR 下就无法编译通过，这就是因为 KEIL 和 IAR 对汇编文件的写法要求是不一样的。KEIL 以及 ADS 下的一些伪指令和写法，在 IAR 下是不支持或者不一样的。具体可以参考 [《EWARM_ADSMigrationGuide.ENU.pdf》](http://ftp.iar.se/WWWfiles/arm/webic/doc/EWARM_ADSMigrationGuide.ENU.pdf)，下文只是我自己在移植过程中的一些修改记录。

<!-- more -->

# 二、修改方法

- 1、修改段和区域的写法

  系统段和区域在 ADS 下定义为 AREA，在 IAR 下定义为 RSEG，因此需要做如下更改。

  > keil 下的写法
  ```asm
  AREA |.text|, CODE, READONLY, ALIGN=2
  ```
  > IAR 下的写法
  ```asm
  RSEG CODE:CODE:NOROOT(2)
  ```

- 2、修改 RN 伪指令

  在 ADS 中，可以使用语句 `name RN Rn` 来给 寄存器 Rn 重命名为 name，在 IAR 下不支持这种写法，因此需要将汇编文件中所有用到的 name 替换回 Rn。类似下面的修改：

  > keil 下的写法

  ```asm
  PCM RN r0  ;rename

  ldr PCM, [sp, #4] ; load pcm pointer
  ```

  > IAR 下的写法
  ```asm
  ldr r0, [sp, #4] ; load pcm pointer
  ```

- 3、修改宏的写法

  ADS 下的宏结束标志和 IAR 下是不同的，另外写法也不一样。具体如下：

  > keil 下的写法
  ```asm
  MACRO
  	MC0S $x
  ldr r12, [r2], #4
  ldr r14, [r2], #4
  ldr r0, [r1, #(4*($x))]
  ldr r3, [r1, #(4*(23 - $x))]

  ...

  MEND ; MCOS
  ```

  > IAR 下的写法
  ```asm
  MC0S MACRO x

      ldr r12, [r2], #4
      ldr r14, [r2], #4
      ldr r0, [r1, #(4*(x))]
      ldr r3, [r1, #(4*(23 - x))]

  ...

  	ENDM ; MCOS
  ```

- 4、删除 PROC、ENP、GBLA 等伪指令。这些指令在 IAR 下不支持，编译无法通过。
