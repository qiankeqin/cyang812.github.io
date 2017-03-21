title: java和c++的区别（一）
tags:
- java
- c++
- 编程
- Idea
- Clion
categories:
- 总结
---

# 在C++中，对参数的引用有两种方式，一种为引用传递，一种为复制传递。

引用传递的方式比较快，因为这种方式不必复制整个参数，同时这种方式可以对参数进行修改。需要在形参声明之前加一个符号（&）来将其变为引用传递。
而复制传递的方式，比较安全，因为在引用函数中不会对原值进行修改。
详情见下面的例子。
<!-- more -->

## 第一种方式
```c++
#include <iostream>

struct Point{
    int x,y;
};

void exchange(Point& p){
    int temp;
    temp = p.x;
    p.x = p.y;
    p.y = temp;
    std::cout << p.x <<"," << p.y << std::endl;
}

int main() {
    Point p1 = {3,4};
    std::cout << p1.x <<","<< p1.y << std::endl;
    exchange(p1);
    std::cout << p1.x <<","<< p1.y << std::endl;
    return 0;
}
```
输出为
```
3,4
4,3
4,3
```

## 第二种方式
将` void exchange(Point& p){` 改成 `void exchange(Point p){`

输出为
```
3,4
4,3
3,4
```

### 可见第一种方式可以对原数值进行更改，而第二种不能。

# 在java中，形参的传递方式为引用传递。详情见下面的例子。
```java
package com.company;

public class Main {

    public static class Point{
        int x;
        int y;

        Point(int x,int y){
            this.x = x;
            this.y = y;
        }
    }

    static void exchange(Point p){
        int temp;
        temp = p.x;
        p.x = p.y;
        p.y = temp;
        System.out.print(p.x+","+p.y);
    }

    public static void main(String[] args) {

       Point p1 = new Point(3,4);
       System.out.print(p1.x+","+p1.y+"\n");
       exchange(p1);
    }
}
```

输出为
```
3,4
4,3
4,3
```
### 可见，函数可对原参数进行修改。
