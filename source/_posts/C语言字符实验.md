title: C语言字符实验
date: 2016/9/7 19:34:23
tags:
- C语言
- 编程
categories:
- 编程
---

## 实验一

代码
```c
#include "stdio.h"

main(){
    int c;
    while( ( c = getchar() )!= '\n' ){
        switch (c-'2'){
            case 0:
            case 1:
                putchar( c+4 );
            case 2:
                putchar( c+4 );
                break;
            case 3:
                putchar( c+3 );
            default:
                putchar( c+2 );
                break;
        }
    }
    printf("\n");
}
```
<!-- more -->

**输入2473<CR>,输出为668977.**

### 知识点
- 1、getchar和putchar都只能操作一个字符。前者为读入一个字符，后者为输出一个字符。

- 2、switch-case的用法，在没有break的情况下，执行完一个case会继续向下执行下一个case，直到遇上break或者switch-case语句结束。也就是说，其实switch-case仅在第一次进入是会进行case的比对，进入switch-case选择结构后不会再进行比对。

## 实验二

使用printf和scanf函数输出和输入字符
- 1、无空格型，例如：
```c
scanf("%c%c%c",&a,&b,&c);
```
输入格式：
```c
ABC<CR> //中间不能有空格，否则空格会被算作字符
```

- 2、空格型，例如：
```c
scanf("%c %c %c",&a,&b,&c);
```
输入格式：
```c
ABC<CR>,A-B-C<CR>,A--B----C<CR> //-表示空格，三种均可
```

- 3、指定输入格式，例如：
```c
scanf("%4c%4c",&c1,&c2);
```
输入格式：
```
A---B---<CR> //必须严格按照指定格式
```

- 4、交叉输入数值数据和字符数据，例如：
```c
int a1,a2; char c1,c2;
scanf("%d%c%d%c",&a1,&c1,&a2,&c2);
```
输入格式：
```c
10A--20B<CR>  //-代表空格，必须按照此格式。
```

### 说明
- 由上述四个例子可知，在使用scanf进行输入时，情况较为复杂，对格式要求很高。尤其是在交叉输入字符和数字时，很容易造成错误。
