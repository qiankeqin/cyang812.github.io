title: C语言 malloc 内存泄漏
date: 2018/3/18 20:34:23
tags:
- C语言
categories:
- 编程
---


错误代码如下：

```c
int Init_layer2_Decoder(void)
{
	Stream = (struct mad_stream*)malloc(sizeof(struct mad_stream));
	Frame = (struct mad_frame*)malloc(sizeof(struct mad_frame));
	Synth = (struct mad_synth*)malloc(sizeof(struct mad_synth));

	if(Stream==NULL || Frame==NULL || Synth==NULL)
	{
		printf("init mp2Dec fail!\n");
		return -1;
	}
		
	mad_stream_init(Stream);
	mad_frame_init(Frame);
	mad_synth_init(Synth);
	
	return 0;
}

```

<!-- more -->

这个函数先为三个结构体变量申请内存空间，其中一个申请失败就返回失败。如果全都申请成功的话，就对结构体变量进行初始化工作。逻辑上似乎没有什么问题，但是这里隐藏了一个内存泄漏的错误。

假如 `Stream` 申请成功，`Frame` 申请失败，满足 if 语句的条件，函数不再继续执行，返回-1。可是 `Stream` 所指向的空间并不会被释放到堆区。这就造成了内存泄漏。类似的情况还有，`Stream` 和 `Frame` 均申请成功，但是 `Synth` 申请失败，此时直接返回，必定会造成内存没有被释放。因此，代码应该做如下修改：

```c
int Init_layer2_Decoder(void)
{
    Stream = (struct mad_stream*)malloc(sizeof(struct mad_stream));
    if(Stream == NULL)
        return -1;

	Frame = (struct mad_frame*)malloc(sizeof(struct mad_frame));
    if(Frame == NULL)
    {
        free(Stream);
        Stream = NULL;
        return -1;
    }

	Synth = (struct mad_synth*)malloc(sizeof(struct mad_synth));
    if(Synth == NULL)
    {
        free(Stream);
        free(Frame);
        Stream = NULL;
        Frame = NULL;
        return -1;
    }    

	mad_stream_init(Stream);
	mad_frame_init(Frame);
	mad_synth_init(Synth);

	return 0;
}

```

这里假设 Frame 大小为100k，整个堆区为75k，因此 Steam 可以申请成功，但 Frame 显然会申请失败。如果不对 Frame 进行释放就直接返回的话，就会造成内存泄漏。如下串口打印可看。
```
**********************
SystemCoreClock = 180000000
sizeof(Stream) = 56
Stream = 0x20003b30
malloc Frame fail!
//free(Stream)
Stream = 0x20003b30
init mp2 dec fail!
---------------------------
total free space = 76736, 74 k
max system bytes =      76800
system bytes     =      76800
in use bytes     =         64
```

从上面的结果可以看到，Stream 的结构大小为56字节，加上必要的用于维护的数据，在堆区占用了64字节， Frame 申请失败后，可看到这64字节并没有被释放。

另外，在对 Stream 进行释放之后，还要将该指针指向 NULL。因为释放只是将该指针指向的内存还给堆区，Stream 指针还是指向堆区，但此时对该指针的指向的区域进行访问是不被允许的。