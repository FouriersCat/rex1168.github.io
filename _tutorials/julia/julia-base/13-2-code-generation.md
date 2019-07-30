---
youtube_id: ult_qjX8D3k
qq_video_id: c08335900v8
b_av: 47775635
b_cid: 83687149
b_page: 41
title: code generation
description: "code generation"
chapter: 13
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 41
---

 
## code generation

在REPL中，我们输入@code来查看用于code generation的宏

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-5.png)

 - @code_lowered: Julia底层的运行过程；
 - @code_typed: 程序运行时type的变化；
 - @code_llvm： llvm编译器的运行过程；
 - @code_native： 生成程序运行的机器语言；
 - @code_warntypes： 查看程序运行时是否有类型上的warning。


首先我们定义一个简单的函数
```Julia
cal(a, b, c) = a + b * c
cal(1, 2, 3)
>>7
cal(1.0, 2.0, 3.0)
>>7.0
cal(1, 2, 3.0)
>>7.0
```

用`@code_lowerd`查看底层运行过程
```Julia
@code_lowered cal(1, 2, 3)
@code_lowered cal(1.0, 2.0, 3.0)
@code_lowered cal(1, 2, 3.0)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-6.png)

可以看出，三个函数运行过程一样，必须是一样的，只是类型不同，但乘和加的过程还是相同的。

再用`code_typed`查看运行时类型变化
```Julia
@code_typed cal(1, 2, 3)
@code_typed cal(1.0, 2.0, 3.0)
@code_typed cal(1, 2, 3.0)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-7.png)

可以看出，前两个运行过程一致，只是输入变量参数不同，计算的函数也相应变化；而第三个函数运行时，多了把两个Int型的变量转为Float变量的过程。

再用`@code_llvm`查看llvm编译器的运行过程
```Julia
@code_llvm cal(1, 2, 3)
@code_llvm cal(1.0, 2.0, 3.0)
@code_llvm cal(1, 2, 3.0)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-8.png)

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-9.png)


可以看出，llvm编译器在对前两个函数运行时，过程基本一致，第三个函数又多了很多步骤。


同样的，用`@code_native`查看机器语言的运行过程。

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-10.png)

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-11.png)


跟`@code_llvm`的结果类似，前两个函数过程基本一致，第三个函数多出很多步骤。

了解了code generation对于我们后续的讲解有所帮助。






