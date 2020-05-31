---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: C++ 内联函数 inline的详细分析
description: ""
chapter: 1
category: base
post-headings:
author-link: #
no-video: true
publish-date: 2019-05-30
thumbnail: /static/img/course_cover-small/cpp.png
index: 2
---




### 1. 什么是内联函数？

&emsp;&emsp;就是使用了关键字`inline`的函数，如

```
inline int max(int a, int b)
{
    return a > b ? a : b;
}
```


### 2. 内联函数有什么作用？

&emsp;&emsp;C++在调用函数时，会执行一系列的操作：
 - 将参数push到堆栈中
 - 将返回地址push到堆栈中
 - 准备返回值
 - 将所有push到堆栈的东西都pop出来

&emsp;&emsp;这一系列的操作无疑增加了延迟，`inline`函数就是把函数内容嵌入到函数调用的地方，但仍然保持函数的独立性（即函数有自己的空间）。
```
int main(){
    int a = 4;
    int b = 3;
    int c = max(a,b);
}
```
在使用`inline`之后，上面的代码在执行过程中，跟下面的代码是一样的：
```
int main(){
    int a = 4;
    int b = 3;
    int c = a > b? a : b;
}
```



### 3. 如何使用内联函数？

&emsp;&emsp;内联函数跟普通函数的使用方式有所不同，内联函数的声明和定义不能分开在不同的文件，即都要在同一个文件中，因此我们在使用时，直接在头文件中定义出`inline`函数即可。

```
inline int max(int a, int b) { return a > b ? a : b;}  //demo.h
```

&emsp;&emsp;看到这里，很多同学就会觉得`inline`跟宏定义的用法很像，比如下面这个例子：

Example1:
```
#define f(x) x*x
int main(){
    double x=2;
    printf("%d", f(x));
}
```
Example2:
```
inline int f(int x) { return x*x;}
int main(){
    double x = 2;
    printf("%d", f(x));
}
```

这两段代码都会产生warning，但第一个代码输出结果是错的，第二个输出结果的对的。这是为什么呢？因为宏定义不会做类型检查，f(x)的结果是一个double型的，而我们用`%d`来打印，结果肯定是不对的；而第二个代码中，x是double型，但函数`f`的参数类型是`int`型，因此会先进行类型转换后，再进行`x*x`的操作。

### 4. 注意事项

 - 如果我们的内联函数过于巨大，那编译器不会将其编译成内联函数；
 - 内联函数中有太多循环，编译器也不会将其编译成内联函数；
 - 递归函数不能是内联函数；
 - 如果class的成员函数，在声明时就给出了函数的body，即函数定义，那这些函数都是`inline`的；如果在成员函数声明时，并没有直接给出body，而是在下面定义成员函数时使用``inline`关键字，这样即可以保持`class`的定义比较干净，还可以使函数类型为`inline`。

### 5. 是否需要inline

什么时候需要`inline`?
 - 当函数比较小的时候，比如只有几行代码，建议使用`inline`
 - 当被循环调用时，建议使用`inline`
 
什么时候不需要`inline`?
 - 当函数超过20行后，就不要再做inline 了
 - 递归函数不能用inline


