title: f_open()使用错误记录
date: 2017/3/10 10:08:23
tags:
- 嵌入式
- FAT32
categories:
- STM32
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

这是因为函数内的变量是定义在栈里面的，而 `FIL` 是 fatfs 中对文件定义的结构体变量，这个变量的内容如下：
```c
/* File object structure (FIL) */

typedef struct {
#if !_FS_TINY
  union{  
	UINT	d32[_MAX_SS/4]; /* Force 32bits alignement */     
	BYTE	d8[_MAX_SS];	/* File data read/write buffer */
  }buf;
#endif
	FATFS*	fs;				/* Pointer to the related file system object (**do not change order**) */
	WORD	id;				/* Owner file system mount ID (**do not change order**) */
	BYTE	flag;			/* Status flags */
	BYTE	err;			/* Abort flag (error code) */
	DWORD	fptr;			/* File read/write pointer (Zeroed on file open) */
	DWORD	fsize;			/* File size */
	DWORD	sclust;			/* File start cluster (0:no cluster chain, always 0 when fsize is 0) */
	DWORD	clust;			/* Current cluster of fpter (not valid when fprt is 0) */
	DWORD	dsect;			/* Sector number appearing in buf[] (0:invalid) */
#if !_FS_READONLY
	DWORD	dir_sect;		/* Sector number containing the directory entry */
	BYTE*	dir_ptr;		/* Pointer to the directory entry in the win[] */
#endif
#if _USE_FASTSEEK
	DWORD*	cltbl;			/* Pointer to the cluster link map table (Nulled on file open) */
#endif
#if _FS_LOCK
	UINT	lockid;			/* File lock ID origin from 1 (index of file semaphore table Files[]) */
#endif

} FIL;

```

可见在最前面有一个共用体，用作读写数据的缓冲，这个其中 `_MAX_SS` 定义为4096。整个结构体的大小，可使用 `sizeof(fil)` 查看，肯定多余4096。可是一般栈都不会有这么大，所以这个问题是由于栈溢出造成的。在函数内部一般不设置大数组，但是保不齐结构体内部有数组。

[参考链接](http://www.openedv.com/posts/list/3161.htm)

