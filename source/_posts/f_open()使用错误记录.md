title: f_open()使用错误记录
date: 2017/3/10 10:08:23
tags:
- 嵌入式
- FAT32
categories:
- 硬件
---

## 一、现象
调用函数 `f_open()` 后，程序崩溃，调试后发现，单片机产生硬件中断，即软件跳入如下部分：
```c
void HardFault_Handler(void)
{
  /* Go to infinite loop when Hard Fault exception occurs */
  while (1)
  {
  	BSP_LED_Toggle(LED3);
  }
}
```

<!-- more -->

## 二、错误代码
代码错误处如下，就是简单的使用 `f_read()` 函数打开一个文件，并将这个文件的前 32 个字节打印出来。
```c
void show_file(uint8_t idx)
{
    FIL fil;
	uint8_t data[32];
	uint8_t bytecounts;
	uint8_t ret;
	
	printf("show file\n");
	
	if((ret = f_open(&fil,(const TCHAR *)File_path,FA_READ)) != FR_OK)
	{
		printf("ret : %d\n",ret);
		SD_Error_Handler();
	}
	else
	{
		if(f_read(&fil, data, sizeof(data), (UINT *)&bytecounts) != FR_OK)
		{
			SD_Error_Handler();
		}
		else
		{
			for(int i=0;i<32;i++)
			{
				printf(" %02x ",data[i]);
			}
			f_close(&fil);
		}
	}
}
```

## 三、解决方法
看了网上的资料，在使用该函数时出现了硬件中断，一般都是单片机内存访问出现了错误。
也就是说在 `f_read(&fil,(const TCHAR *)File_path,FA_READ)` 的参数中，无法找到 `fil` 的地址。将 `FIL fil;` 从函数外拿出，定义为全局变量，即可解决。

[参考链接](http://www.openedv.com/posts/list/3161.htm)

