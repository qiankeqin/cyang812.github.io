title: C语言 ringBuffer
date: 2018/3/22 20:32:23
tags:
- C语言
- ringBuffer
categories:
- 编程
---

# 一、 ringBuffer 介绍

ringBuffer 称作环形缓冲，也有叫 circleBuffer 的。就是取内存中一块连续的区域用作环形缓冲区的数据存储区。这块连续的存储会被反复使用，向 ringBuffer 写入数据总是从写指针的位置开始，如写到实际存储区的末尾还没有写完，则将剩余的数据从存储区的头开始写；从该 ringBuffer 读出数据也是从读指针的位置开始，如读到实际存储区的末尾还没有读完，则从存储区的头开始读剩下的数据。

<!-- more -->

为了保证写入的数据不会覆盖 ringBuffer 里还没有被读出的数据，以及读出的数据不是已经读出过的旧数据，需要使用一个变量 btoRead 表示该 ringBuffer 中有效的数据。使用变量 length 表示该环形缓冲区中真实的缓冲大小。使用指针 source 指向实际的缓存地址。

使用 ringBuffer 读写数据，要确保读写数据的速率和实际缓冲区大小的匹配。如果不匹配，可能会导致溢出，比如读数据太慢，而写数据很快，实际的缓存区又太小，导致整个缓冲区都是还没有被读出的数据，此时新的数据就无法写入。正确使用 ringBuffer 可以保证数据的连续，降低读模块和写模块之间的耦合性。更多关于生产者-消费者模型的知识可以看这篇[博客](https://blog.csdn.net/lenyusun/article/details/6609786)。

# 二、代码

ringBuffer 的结构体
```c
typedef struct {
    uint8_t *source;
    uint32_t br;
    uint32_t bw;
    uint32_t btoRead;
    uint32_t length;
}ringbuffer_t;
```

创建 ringBuffer 函数
```c
void create_ringBuffer(ringbuffer_t *ringBuf, uint8_t *buf, uint32_t buf_len)
{
	ringBuf->br         = 0;
	ringBuf->bw         = 0;
	ringBuf->btoRead    = 0;
	ringBuf->source     = buf;
	ringBuf->length     = buf_len;
	printf("create ringBuffer->length = %d\n", ringBuf->length);
}
```

清空 ringBuffer 函数
```c
void clear_ringBuffer(ringbuffer_t *ringBuf)
{
	ringBuf->br         = 0;
	ringBuf->bw         = 0;
	ringBuf->btoRead    = 0;
	
	//no need do this casue r_ptr and w_prt has change
//	memset((uint8_t *)ringBuf->source, 0, ringBuf->length); 
}
```

读数据函数
```c
uint32_t write_ringBuffer(uint8_t *buffer, uint32_t size, ringbuffer_t *ringBuf)
{
	uint32_t len            = 0;
	uint32_t ringBuf_bw     = ringBuf->bw;
	uint32_t ringBuf_len    = ringBuf->length;
	uint8_t *ringBuf_source = ringBuf->source;
	
	if( (ringBuf_bw + size) <= ringBuf_len  )
	{
		memcpy(ringBuf_source + ringBuf_bw, bufff, size);
	}
	else
	{
		len = ringBuf_len - ringBuf_bw;
		memcpy(ringBuf_source + ringBuf_bw, buffer, len);
		memcpy(ringBuf_source, buffer + ringBuf_bw, size - len);
	}

	ringBuf->bw = (ringBuf->bw + size) % ringBuf_len;
	ringBuf->btoRead += size;

	return size;
}
```

写数据函数
```c
uint32_t read_ringBuffer(uint8_t *buffer, uint32_t size, ringbuffer_t *ringBuf)
{
	uint32_t len            = 0;
	uint32_t ringBuf_br     = ringBuf->br;
	uint32_t ringBuf_len    = ringBuf->length;
    uint8_t *ringBuf_source = ringBug->source;

	if( (ringBuf_br + size ) <= ringBuf_len )
	{
		memcpy(buffer, ringBuf_source + ringBuf_br, size);
	}
	else
	{
		len = ringBuf_len - ringBuf_br;
		memcpy(bufff, ringBuf_source + ringBuf_br, len);
		memcpy(buffer + len, ringBuf_source, size - len);
	}

	ringBuf->br = (ringBuf->br + size) % ringBuf_len;
	ringBuf->btoRead -= size;

	return size;
}
```

获取 ringBuffer 中的有效数据
```c
uint32_t get_ringBuffer_btoRead(ringbuffer_t *ringBuf)
{
	return ringBuf->btoRead;
}
```

获取 ringBuffer 的长度
```c
uint32_t get_ringBuffer_length(ringbuffer_t *ringBuf)
{
	return ringBuf->length;
}
```

# 三、使用方法
对 ringBuffer 的使用，首先需要又一块真实并且连续的数据存储区。可以使用 malloc 从堆区分配，也可以使用一个数组。

在写数据之前，需要对此时 ringBuffer 的剩余空间和要写入数据的大小进行比较。剩余空间使用长度 length 减去待读出数据量 btoRead 得到。

在读出数据之前，则需要对此时 ringBuffer 可读出的有效数据 btoRead 进行判断。

读出的数据不够，或者没有足够的空间写如数据，可以在调用读写函数之前进行判断，假如情况不满足，就不调用相应的读写函数。