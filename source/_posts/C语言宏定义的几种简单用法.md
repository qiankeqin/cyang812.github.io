title: C语言宏定义的几种简单用法
date: 2016/12/2 21:13:23
tags:
- C语言
categories:
- 编程
---

- 1、计算数组的大小

  ```c
  #define countof(a) (sizeof(a)/sizeof(*(a)))
  ```

<!-- more -->

- 2、转换大小写字母

  ```c
  #define FS_TOUPPER(x) ((((x) >= 'a') && ((x) <= 'z')) ? (x) - 'a' + 'A' : (x))
  #define FS_TOLOWER(x) ((((x) >= 'A') && ((x) <= 'Z')) ? (x) - 'A' + 'a' : (x))
  ```

- 3、大小端模式转换

  ```c
  #define SWAP16(x) (((x) << 8) & 0xff00) | (((x) >> 8) & 0xff)
  #define SWAP32(x) ((((uint32_t)(x) >> 24) & 0xff) | (((uint32_t)(x) >> 8) & 0xff00) | (((uint32_t)(x) << 8) & 0xff0000) | (((uint32_t)(x) << 24) & 0xff000000))

  ```

- 4、生成字，双字

  ```c
  #define MAKE_DWORD(b3, b2, b1, b0) (((uint32_t)((uint8_t)(b3) << 24)) | ((uint8_t)(b2) << 16) | ((uint8_t)(b1) << 8) | ((uint8_t)(b0)))
  #define MAKE_WORD(h, l)  (((uint32_t)(h) << 8) | ((l) & 0xffff))

  ```

- 5、获取高，低字节

  ```c
  #define GET_WORD_HIGH(x) ((uint32_t)(x) >> 16)
  #define GET_WORD_LOW(x)  ((uint32_t)(x) & 0xffff)
  #define GET_WORD_HIGHBYTE(x) ((uint32_t)(x) >> 24)
  #define GET_WORD_LOWBYTE(x)	((uint32_t)(x) & 0xff)

  ```

## 以上宏定义的示例程序

```c
#include <stdio.h>
#include <stdint.h>

#define countof(a) (sizeof(a)/sizeof(*(a)))

#define FS_TOUPPER(x) ((((x) >= 'a') && ((x) <= 'z')) ? (x) - 'a' + 'A' : (x))
#define FS_TOLOWER(x) ((((x) >= 'A') && ((x) <= 'Z')) ? (x) - 'A' + 'a' : (x))

#define SWAP16(x) (((x) << 8) & 0xff00) | (((x) >> 8) & 0xff)
#define SWAP32(x) ((((uint32_t)(x) >> 24) & 0xff) | (((uint32_t)(x) >> 8) & 0xff00) | (((uint32_t)(x) << 8) & 0xff0000) | (((uint32_t)(x) << 24) & 0xff000000))

#define MAKE_DWORD(b3, b2, b1, b0) (((uint32_t)((uint8_t)(b3) << 24)) | ((uint8_t)(b2) << 16) | ((uint8_t)(b1) << 8) | ((uint8_t)(b0)))
#define MAKE_WORD(h, l)  (((uint32_t)(h) << 8) | ((l) & 0xffff))

#define GET_WORD_HIGH(x) ((uint32_t)(x) >> 16)
#define GET_WORD_LOW(x)  ((uint32_t)(x) & 0xffff)
#define GET_WORD_HIGHBYTE(x) ((uint32_t)(x) >> 24)
#define GET_WORD_LOWBYTE(x)	((uint32_t)(x) & 0xff)

void test_countof(){

	int a[10] = {};
	printf("%d\n",countof(a));
}

void test_FSTOUPPER(){

	char a[14] = "This is a test";
	char ans_up[14];
	char ans_low[14];

	for (int i = 0;i<14;i++){
		ans_up[i] = FS_TOUPPER(a[i]);
		printf("%c",ans_up[i]);
	}

	printf("\n");

	for (int j = 0; j<14; j++){
		ans_low[j] = FS_TOLOWER(ans_up[j]);
		printf("%c",ans_low[j]);
	}
}

void test_SWAP(){
	uint16_t x = 0x1234;
	uint32_t y = 0x12345678;

	printf("%x\n",SWAP16(x));
	printf("%x\n",SWAP32(y));
}

void test_MAKE_WORD(){

	uint8_t a = 0x12;
	uint8_t b = 0x34;
	uint8_t c = 0x56;
	uint8_t d = 0x78;

	printf("%x\n",MAKE_WORD(a,b));
	printf("%x\n",MAKE_DWORD(a,b,c,d));
}

void test_GET_WORD(){

	uint32_t a = 0x12345678;

	printf("%x\n",GET_WORD_HIGH(a));
	printf("%x\n",GET_WORD_LOW(a));

	printf("%x\n",GET_WORD_HIGHBYTE(a));
	printf("%x\n",GET_WORD_LOWBYTE(a));
}

void main(void) {

	test_countof();

	test_FSTOUPPER();

	test_SWAP();

	test_MAKE_WORD();

	test_GET_WORD();
}
```
