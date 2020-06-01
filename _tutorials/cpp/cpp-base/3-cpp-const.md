---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: C++ const使用教程，有哪些需要注意的地方？
description: ""
chapter: 1
category: cpp-base
post-headings:
author-link: #
no-video: true
publish-date: 2019-05-30
thumbnail: /static/img/course_cover-small/cpp.png
index: 3
---



&emsp;&emsp;常量在C++中经常用到，用关键字`const`表示，它是常数变量，也就是说，它仍然是**变量**，而不是**常数**。什么区别呢？编译器会为变量在内存中分配地址空间，而常数是编译器在编译过程中记录在内存表里一个实体。

- 常量定义
```
const int a = 10;
```
在定义完之后，就不能再修改常量`a`的值了。如果是在不同的文件中使用，需要用到关键字`extern`
```
extern const int a ;
```
这样表示变量`a`是一个定义别处的全局变量，在这里只能使用，并不能修改，但`a`并不一定真的是一个`const`。

**指针常量**和**常量指针**
&emsp;&emsp;这应该是C++面试和笔试中经常考到的一个知识点。

 - 指针常量
```
int *const q = 10;  // q is const
*q = 20;    // OK
q++;        // ERROR
```
指针常量，在上面的代码中，q是常量，指向的地址是不能变化的，但指向地址中的内容是可以改变的。

 - 常量指针
 
```
int a = 10;
int b = 20;
const int *p = &a;      // *p is const
*p = 15;                // ERROR 
a = 30;                 // OK
p = &b;                 // OK
```
常量指针，在上面的例子中，(*p)是常量，我们不能通过(*p)来改变指向的对象即a；但a本身是可以改变的，而且p也是可以指向别处的。

有什么好办法容易区分这两个呢？可以看`const`和`*`的先后顺序：
```
const int *p;  //常量指针
int *const p; // 指针常量
```
而且只要我们记住，`const`后面的是常量，这样就很容易区分它们的功能，对于**常量指针**，`*p`都在`const`后面，所以`*p`是个常量，直接对`*p`赋值的操作肯定是错误的；而**指针常量**，只有`p`在`const`后面，所以`p`是常量，指针指向地址是不能变的，但指向地址里面的内容是可以变的。

**函数参数中的常量**
如果函数的参数中有常量，那么在函数中，该参数是不能被修改的
```
int func(const int i){
    i++;  // ERROR
}
```

**函数返回值为常量**
```
int f1() {return 1;}
const int f2() {return 1;}
int main(){
    const int k = f1();     // OK
    int n = f2();              // OK
}
```

所以，对于const变量，我们可以要记住一个核心的一点是，const后面的变量是不能当左值的。