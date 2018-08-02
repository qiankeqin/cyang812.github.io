title: fread 返回 0
date: 2018-07-25 20:56:19
tags:
- C语言
categories:
- 编程
---

fread 函数一直返回 0，检查过读取的数量不会超过文件大小，错误发生在打开文件时错误。

错误代码如下：
```c
FILE *in_file, *out_file;
unsigned int open_files(const char *in_file_name, const char *out_file_name)
{
	if( in_file = fopen(in_file_name, "rb") == NULL)

		return 0;

	if( out_file = fopen(out_file_name, "wb") == NULL)

		return 0;

	return 1;
}
```

<!-- more -->

正确应该是：
```c
FILE *in_file, *out_file;
unsigned int open_files(const char *in_file_name, const char *out_file_name)
{
	if( (in_file = fopen(in_file_name, "rb")) == NULL)

		return 0;

	if( (out_file = fopen(out_file_name, "wb")) == NULL)

		return 0;

	return 1;
}
```

错误的原因在于没有使用括号，而比较运算符 == 的优先级比赋值运算符 = 要高。因此，错误的程序执行结果为，函数正常打开了文件，返回的文件指针与 NULL 相比为0，赋值给了 in_file, if 不会执行，因此误以为 in_file 是文件指针，实际上为0，所以无法读出数据。
