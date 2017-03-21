title: FatFs 使用中文长文件名
tags:
- 嵌入式
- FAT32
categories:
- 硬件
---

## 一、说明

![这里写图片描述](http://img.blog.csdn.net/20170224132232460?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

<!-- more -->

使用长文件名，一般会是在使用 `f_readdir()` 这个函数时碰到，这个函数的功能就是获取上一步使用 `f_opendir()` 打开的文件夹中的内容，并将文件信息保存到定义的结构体。


结构体内容如下，
![这里写图片描述](http://img.blog.csdn.net/20170224133146238?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
里面包含有文件大小，上一次修改日期，文件属性，文件名等。可见，普通文件名是存在一个 `fname[13]` 的数组里的，这就使得长文件名无法正常显示。而长文件名是一个指针，这个指针指向的数组是需要自己定义的。

## 二、方法

在使用长文件名时，需要更改 ffconf.h 中的宏定义如下，
![这里写图片描述](http://img.blog.csdn.net/20170224132258711?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如果需要支持中文则还需要做如下更改，
![这里写图片描述](http://img.blog.csdn.net/20170224132319663?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**如下内容非常关键：**

### 方式1
使用长文件名时，需要自己添加存储长文件名的 buffer, 所以需要在用户程序中定义如下内容；
![这里写图片描述](http://img.blog.csdn.net/20170224132405274?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
将文件信息中长文件名指针指向定义的 buffer
![这里写图片描述](http://img.blog.csdn.net/20170224132431648?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 方式2
也可以直接使用这种方式：
![这里写图片描述](http://img.blog.csdn.net/20170224192936262?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

效果如下：
![这里写图片描述](http://img.blog.csdn.net/20170224132524589?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## 三、演示代码

附一份示例代码：
```c
void scan_files(void)
{
#if _USE_LFN
  Fileinfo.lfname = lfn;
  Fileinfo.lfsize = sizeof(lfn);
#endif
	uint8_t ret;
	uint8_t sum = 0;

	if(FATFS_LinkDriver(&SD_Driver, SDPath) == 0)
  {
    if(f_mount(&SDFatFs, (TCHAR const*)SDPath, 0) != FR_OK)
    {
      SD_Error_Handler();
    }
    else
    {
			if((ret = f_opendir(&Dir,(const TCHAR *)MUSIC_DIR_PATH)) != FR_OK)
			{
				printf("ret : %d ",ret);
				SD_Error_Handler();
			}
			else
			{
				printf("open music dir\n");
				for(;;)
				{
					ret = f_readdir(&Dir,&Fileinfo);
					if(ret != FR_OK || Fileinfo.fname[0] == 0)
					{
						break;//Break on error or end of dir
					}
					else
					{
						if(Fileinfo.lfname[0] == 0)
						{
							printf("lfname : error\n");
						}
						else
						printf("%s\n",Fileinfo.lfname);
					}
					if(Fileinfo.fattrib & AM_ARC)//is a file?
					{
							strcpy((char *)filename[sum],(char *)Fileinfo.lfname);
							sum++;
					}
				}
			}
		}
	}
	printf("sum : %d \n",sum);
	show_filename(sum);
	//show_file();
	FATFS_UnLinkDriver(SDPath);
}
```
