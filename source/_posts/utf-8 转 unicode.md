title: utf-8 to unicode
date: 2017/5/17 00:07:23
tags:
- 编程
- C
- Unicode
categories:
- 嵌入式
---

# 一、utf-8 unicode utf-16 
- 1、unicode 使用两字节表示字符。
- 2、utf-8 和 utf-16均为变长编码，使用1~4个字节来表示字符。
- 3、utf-8 和 utf-16是不一样的，汉子使用 unicode 表示是两个字节，utf-8 是三个字节，utf-16 是两个字节。
- 4、utf-8 只是 unicode的一种实现方式，类似的方式还有 utf-16 和 utf-32。
- 5、Unicode是国际组织制定的可以容纳世界上所有文字和符号的字符编码方案。目前的Unicode字符分为17组编排，0x0000 至 0xFFFF，每组称为平面（Plane），而每平面拥有65536个码位，共1114112个。然而目前只用了少数平面。UTF-8、UTF-16、UTF-32都是将数字转换到程序数据的编码方案。
- 6、UCS-2用两个字节编码，UCS-4用4个字节编码。

<!-- more -->

# 二、utf-8 和 unicode 的对应关系
<span xmlns="http://www.w3.org/1999/xhtml" style="">// #txt---  
   |  Unicode符号范围      |  UTF-8编码方式  
 n |  (十六进制)           | (二进制)  
---+-----------------------+------------------------------------------------------  
 1 | 0000 0000 - 0000 007F |                                              0xxxxxxx  
 2 | 0000 0080 - 0000 07FF |                                     110xxxxx 10xxxxxx  
 3 | 0000 0800 - 0000 FFFF |                            1110xxxx 10xxxxxx 10xxxxxx  
 4 | 0001 0000 - 0010 FFFF |                   11110xxx 10xxxxxx 10xxxxxx 10xxxxxx  
 5 | 0020 0000 - 03FF FFFF |          111110xx 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx  
 6 | 0400 0000 - 7FFF FFFF | 1111110x 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx  
  
// #txt---end  
</span>  

# 三、C语言实现 utf-8 to unicode

```c
#include <stdio.h>
#include "stdint.h"

#define DLS_LEN		(uint8_t)129
#define DWBYTE(b3, b2, b1, b0) (((uint32_t)((uint8_t)(b3) << 24)) | ((uint8_t)(b2) << 16) | ((uint8_t)(b1) << 8) | ((uint8_t)(b0)))

void UTF8ToUnicode(uint8_t *UTF8,uint16_t *Unicode)
{
	uint16_t i=0,j=0;
	uint8_t buf[4];
	
	while(UTF8[i])
	{
		if((UTF8[i]&0x80)==0x00)
		{
			Unicode[j]=(uint16_t)DWBYTE(0,0,0,UTF8[i]);
			i+=1;
		}
		else if((UTF8[i]&0xe0)==0xc0)
		{
			buf[1]=(UTF8[i]&0x1c)>>2;
			buf[0]=(UTF8[i]<<6)|(UTF8[i+1]&0x3f);
			Unicode[j]=(uint16_t)DWBYTE(0,0,buf[1],buf[0]);
			i+=2;
		}
		else if((UTF8[i]&0xf0)==0xe0)
		{
			buf[1]=(UTF8[i]<<4)|((UTF8[i+1]&0x3c)>>2);
			buf[0]=(UTF8[i+1]<<6)|(UTF8[i+2]&0x3f);
			Unicode[j]=(uint16_t)DWBYTE(0,0,buf[1],buf[0]);
			i+=3;
		}
		else if((UTF8[i]&0xf8)==0xf0)
		{
			buf[2]=((UTF8[i]&0x07)<<2)|((UTF8[i+1]&0x30)>>4);
			buf[1]=((UTF8[i+1]&0x0f)<<4)|((UTF8[i+2]&0x3c)>>2);
			buf[0]=(UTF8[i+2]<<6)|(UTF8[i+3]&0x3f);
			Unicode[j]=(uint16_t)DWBYTE(0,buf[2],buf[1],buf[0]);
			i+=4;
		}
		else
		{
			Unicode[j] = (uint16_t)UTF8[i];
			i++;
		}
		if(Unicode[j]<0x20)
			Unicode[j]=0x20;
		j++;
		if(i>=(DLS_LEN-1))
			break;
	}
	if(j>=(DLS_LEN-1))
		Unicode[DLS_LEN-1]=0x00;
	else
		Unicode[j]=0x00;
}

int main()
{
	uint8_t xx=0;

	uint8_t utf8[256] = {0xE6,0xA2,0x81,0xE9,0x9D,0x99,0xE8,0x8C,0xB9};
	uint16_t unicode[256];
	
	UTF8ToUnicode(utf8,unicode);

	for(xx; xx<20; xx++)
	{
		printf("unicode[%d]:0x%x\n",xx,unicode[xx]);
	}
	return 0;
}
```
