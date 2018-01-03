title: IAR DLIB Library heap usage statistics IAR heap 分析
date: 2018/1/3 20:49:23
tags:
- 编程
- IAR
- STM32
categories:
- 嵌入式
---

> 翻译自 [IAR Technical Note 28545 《IAR DLIB Library heap usage statistics》](https://www.iar.com/support/tech-notes/general/iar-dlib-library-heap-usage-statistics/) update 2017/9/22

# 介绍
关于堆的描述在《IAR C/C++ Development Guide for ARM》的 Dynamic memory on the heap 一章中。本技术手册仅描述在应用程序中如何统计堆的使用量。通过跟踪 malloc 或类似函数使用的内存总量来实现。

<!-- more -->

# 讨论
在 IAR Embedded Workbench for ARM 6.60 及其以后的版本中，你可以使用如下函数来收集 heap 的使用量：
```c
__iar_dlmallinfo()
```
如果想要一个简单的输出示例，调用如下函数：
```c
__iar_dlmalloc_stats()
```
这个函数的输出示例如下：
```c
max system bytes =     2048
system bytes     =     2048
in use bytes     =       16
```
你可以在IAR安装目录下的  `arm\src\lib\dlib\heap\dlmalloc.c` 和 `arm\src\lib\dlib\heap\dlmalloc_stat.c` 文件中找到上述函数的声明和定义。如果是IAR for ARM 8.10 以前的版本，这两个文件应该在 arm\src\lib 目录下。

为了能够调用这些函数，你需要包含头文件  mallocstats.h 。你可以在[这里](https://www.iar.com/contentassets/680ee3f178b14736acaadc62549b2977/mallocstats_test.zip)下载一个 zip 文件，里面有这个头文件和一个 main_test.c 文件，用于演示如何调用上述函数。

# 历史版本
对于IAR Embedded Workbench for ARM 6.60 以前的版本中，你必须将 arm\src\dlmalloc.c 加入到工程中，并且设置宏 NO_MALLINFO 和 NO_MALLOC_STATS 为 0 。注意，你需要将这个文件拷贝到你的项目目录下进行修改。并且，如果是在 C++ 工程中，你需要使用 C编译器 来编译这个文件。

# 堆最大使用量
以下内容摘自文章《Mastering stack and heap for system reliability》的如何设置堆大小一节。

> 桌面系统中，通过实现 sbrk() ，堆的最大使用量可以从 malloc_max_footprint() 得到。嵌入式系统并不实现 sbrk()，内存分配器通常只在一块内存上进行分配。因此 malloc_max_footprint() 函数是没用的，它仅仅返回整个 heap 的大小。

IAR Embedded Workbench 并不实现 sbrk()。

在这篇文章中提到了一些关于如何计算程序使用的 heap 大小的方法和技巧。

# 结论
本技术手册介绍了如何在应用程序中统计 heap 的使用量。

所有产品名称均为其各自所有者的商标或注册商标。


