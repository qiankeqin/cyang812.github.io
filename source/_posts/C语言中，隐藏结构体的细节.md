title: C语言中，隐藏结构体的细节
date: 2018-04-18 17:49:19
tags:
- C语言
categories:
- 编程
---

本文转载自 [博客园](https://www.cnblogs.com/qingergege/p/6882107.html)

我们都知道，在C语言中，结构体中的字段都是可以访问的。或者说，在C++ 中，类和结构体的主要区别就是**类中成员变量默认为private，而结构体中默认为public**。结构体的这一个特性，导致结构体中封装的数据，实际上并没有封装，外界都可以访问结构体中的字段。

<!-- more -->

C++中我们尚可用类来替代结构体，但是，C语言中是没有类的，只能用结构体，但很多时候，我们需要隐藏结构体的字段，**不让外界直接访问，而是通过我们写的函数进行间接访问**，这样就提高了程序的封装性。 

实现方法，简单来说，就是，结构体定义时，要定义在.c文件中，然后我们自己定义一些访问结构体的函数，在.h文件中，只存放函数原型声明和对结构体的声明。

看个例子
.c文件中
```c
//stu.c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct stu{
    char id[10];
    int score;
};

struct stu *new_stu()
{
    struct stu *s;
    s = (struct stu *)malloc(sizeof(struct stu));
    

    return s;
}

void set_id(struct stu *s,char *id)
{
    strcpy(s->id,id);
}

char *get_id(struct stu *s)
{
    return s->id;
}
```
可以看到，在.c文件中，我定义了一个结构体，并且定义了一些用于操作这个结构体的函数。

在.h文件中
```c
stu.h
#ifndef STU_H
#define STU_H

struct stu;
extern void set_id(struct stu *s,char *id);
extern char *get_id(struct stu *s);

extern struct stu *new_stu();

#endif
```

在.h中我声明了一下结构体struct stu，并且写了函数的原型声明，供其他文件调用。

在main.c中我引用了stu.h

 

下面是main.c
```c
#include <stdio.h>
#include "stu.h"

int main()
{
    //struct stu s;
    //s.score = 100;
    //struct stu s = {{0}};
    
    struct stu *s;
    s = new_stu();
    
    set_id(s, "950621");
    char *id = NULL;

    id = get_id(s);

    printf("设置的id为:%s\n",id);
    return 0;
}
```

可以看到，在main函数中，我先是定义了一个struct stu类型的指针，然后通过new_stu()给这个指针分配了空间，在通过另外两个函数对其进行了操作。

 

这里需要注意一下我注释掉的部分，说明一下：

这种情况下，**不能定义struct stu类型的变量！！！**

因为：

.h文件中，**只是对结构体进行了声明**，并没有结构体具体细节的描述，也就是在main.c中只是声明了一下struct stu，这样编译器就知道有个结构体类型叫struct stu，**但是它并不知道stu的内部细节**。

我们都知道，定义一个变量，编译器是要给它**分配内存空间的**，但是，**此时编译器并不知道stu的内部细节**，也就不知道stu这个结构体的变量要占多少空间，自然无法分配内存。这样在编译时期就会报错。

但是定义一个指针变量就不一样啦，不管是什么类型的指针，占据的内存空间都是4个字节，编译器只需要确定有个叫struct stu 的类型存在就好了，而.h中那个声明，就是在告诉编译器，有这么一个类型。

 

同时，这种情况下也不能访问结构体的字段，比如，s->score=100;这条语句在编译时就会报错，原因和上面一样，**编译器并不知道struct stu结构体的内部细节**。

 

通过上面的方法，在除了stu.c文件之外的其他文件中，**只能通过stu.c中定义的函数来间接操作结构体变量，而不能直接对结构体变量进行操作，**包括不能创建一个结构体变量！

这样就很好地体现了程序的封装性，也提高了程序的安全性。但是就需要我们写很多操作函数啦，包括创建结构体指针变量分配空间的函数。

 

我们也可以在.h文件中用typedef声明一个结构体的指针类型，如  typedef struct sut * pStu;

这样在main.c中就可以用pStu声明结构体指针变量了。