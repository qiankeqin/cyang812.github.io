title: STM32 无法通过 百分号f 打印浮点数
date: 2018/3/3 14:01:23
tags:
- 编程
- IAR
- STM32
categories:
- 嵌入式
---

# 一、问题
使用 IAR 开发 STM32，发现无法通过 printf 重定向到串口打印出浮点数。代码如下：
![这里写图片描述](http://img.blog.csdn.net/20180302144541975?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

<!-- more -->

输出结果如下：
![这里写图片描述](http://img.blog.csdn.net/20180302144722631?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可见，浮点数部分无法正常显示。

# 二、解决方法
这是由于 IAR 默认选择的 printf 库不支持浮点数的的输出。可在设置选项中修改。如下：默认使用 small，改为 auto 即可。
![这里写图片描述](http://img.blog.csdn.net/20180302144931419?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 三、备注
在不修改设置的情况下，尝试过如下两种代码解决。一种是通过 sprintf 将浮点数转换成字符串输出，另一种是分解整数和小数部分，分别输出。第一种方法也是不可行的，只有分解可以。

代码如下：

```c
/*
* cyang 2018/2/27
* mcu printf float value
*/

#include <stdio.h>

void printf_float(float a)
{
	char tmp[8]={0};
	int i;
	sprintf(tmp, "%f", a);
	for(i=0; i<8; i++)
		printf("%c", tmp[i]);
	printf("\n");
}

void PrintFloat(float value)
{
    int tmp,tmp1,tmp2,tmp3,tmp4,tmp5,tmp6;
    tmp = (int)value;
    tmp1=(int)((value-tmp)*10)%10;
    tmp2=(int)((value-tmp)*100)%10;
    tmp3=(int)((value-tmp)*1000)%10;
    tmp4=(int)((value-tmp)*10000)%10;
    tmp5=(int)((value-tmp)*100000)%10;
    tmp6=(int)((value-tmp)*1000000)%10;
    printf("f-value=%d.%d%d%d%d%d%d\r\n",tmp,tmp1,tmp2,tmp3,tmp4,tmp5,tmp6);
}

int main(int argc, char const *argv[])
{
	/* code */
	float a = 2.354954;
	printf("a = %f\n", a);

	printf_float(a);
	PrintFloat(a);

	return 0;
}
```