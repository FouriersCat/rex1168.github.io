---
youtube_id: y9jbF9whZJE
qq_video_id: r08307w41sw
b_av: 47775635
b_cid: 83681145
b_page: 2
title: Julia简介
description: "Julia是在什么样的环境下诞生的？Julia的应用场景都有哪些？"
chapter: 1
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 2
---

 
# Julia简介

参考
[维基百科](https://zh.wikipedia.org/wiki/Julia_(%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80))

**历史**


一群拥有各种语言丰富编程经验的Matlab高级用户，对现有的科学计算编程工具感到不满——这些软件对自己专长的领域表现得非常棒，但在其它领域却非常糟糕。他们想要的是一个开源的软件，它要像C语言一般快速而有拥有如同Ruby的动态性；要具有Lisp般真正的同像性而又有Matlab般熟悉的数学记号；要像Python般通用、像R般在统计分析上得心应手、像Perl般自然地处理字符串、像Matlab般具有强大的线性代数运算能力、像shell般胶水语言的能力，易于学习而又不让真正的黑客感到无聊；还有，它应该是交互式的，同时又是编译型的……

**特点**

 - 核心语言非常小。标准库用的是Julia语言本身写的
 - 调用许多其它成熟的高性能基础代码。如线性代数、随机数生成、快速傅里叶变换、字符串处理。
 - 丰富的用于创建或描述对象的类型语法
 - 高性能，接近于静态编译型语言。包括用户自定义类型等
 - 为并行计算和分布式计算而设计
 - 轻量级协程
 - 优雅的可扩展的类型转换/提升
 - 支持Unicode，包括但不限于UTF-8
 - 可直接调用C函数（不需要包装或是借助特殊的API）
 - 有类似shell的进程管理能力
 - 有类似Lisp的宏以及其它元编程工具
 


**使用场景**

主要用于科学计算

 - 机器学习
 - 数据处理
 - 算法仿真
 - 数值分析
 - etc

很多做算法的朋友应该都有这样的经历，在做一个项目时，先用Python/Matlab完成算法模型验证，再用其他编程语言（如C++）来实现，而有了Julia，我们直接一步到位，模型验证和实现是一起的。

**说明**
本教程主要参考：
 - [Julia](https://julialang.org/)
 - [Mapt网站教程（收费的）](https://mapt.packtpub.com)
 - [Julia社区](https://discourse.julialang.org/)
 - [Julia中文社区](http://discourse.juliacn.com/)

**缺点**

参考[简书](https://www.jianshu.com/p/05f07f012c76)
每个语言都有优缺点，Julia的缺点在于：

 - 基于JIT（Just-in-time，即时编译器，启动有预热时间，不适合小规模、只运行一次的任务；
 -  新语言的生态还不够强，不适合调库党；
 -  CLI（command-line interface，命令行界面）工具缺乏，不适合在终端（Terminal）开发，需要借助能够热重启（reload）和IDE开发。

这是Julia官网给出的各种语言之间的速度评测，C的速度为10^0。可以看出，Julia的运行时间基本是除了C之外最快的，有的算法甚至比C还要快。


![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/1-1.png)





