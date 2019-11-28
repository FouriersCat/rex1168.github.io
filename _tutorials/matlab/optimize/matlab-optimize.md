---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Matlab高效编程技巧
description: "."
chapter: 1
category: optimize
post-headings:
author-link: #
no-video: true
publish-date: 2019-11-25
thumbnail: /static/img/course_cover-small/matlab-skills.png
index: 1
---





&emsp;&emsp;用过Matlab的同学应该都知道，Matlab的慢是出了名的，但是再慢也有优化的方式，下面我们给出几个Matlab编程中常用的优化技巧。

&emsp;&emsp;在讲优化方法之前，首先要说的就是Matlab中用`tic toc`的方式来计算运行时间，这是个常识。当然，想统计具体的耗时，可以用`profile`工具。


1.向量化操作

&emsp;&emsp;这个应该是用过Matlab的同学都清楚的一点，Matlab中操作向量和矩阵的速度要比使用for循环的速度快很多，是因为其底层调用了高性能线性代数库BLAS库和LAPACK库。这个就不多说了。



2.内存预分配

&emsp;&emsp;在Matlab中我们可以定义一个空矩阵
```
mtx = [];
```
然后后面再给它加入一些数据，而且这个矩阵大小可以随着我们填入数据的多少而变化。像下面这个程序
```
tic
n = 1000;
mtrx = [];
init = 1.0;
for i = 1:n
    for j=1:n
        mtrx(i,j) = init + 1.0;
    end
end
toc
```
这个程序的运行时间是多久呢？在我电脑上是0.2秒。

&emsp;&emsp;那这个程序有什么问题呢？就是我们没有为这个矩阵分配一个内存空间，而且在循环中，矩阵大小是变化的，这就导致每次循环时都浪费额外的时间去寻找满足需求的内存空间，将改变大小后的矩阵整体移动到这个新的内容空间中，并释放原来的内存空间，这除了会影响代码的运行效率，还容易形成内存碎片，让程序越来越难找到满足条件的内存。

&emsp;&emsp;因此在循环前给矩阵预分配内存是很一个良好的习惯，如果没有这个习惯，你还可以通过Matlab自带的代码检查器来查看是否存在类似问题。

所以，我们应该把程序修改如下：
```
tic
n = 1000;
mtrx = zeros(n,n);
init = 1.0;
for i = 1:n
    for j=1:n
        mtrx(i,j) = init + 1.0;
    end
end
toc
```
这个程序只用了0.007秒的时间就运行完成了，可见它们的差距有多大。

3.按列存储

&emsp;&emsp;Matlab中默认是按列存储的，也就是说，列向量在内存中是连续排列的，对连续的数据做处理肯定是要快的，所以我们在定义向量时一般都会使用列向量。下面对比矩阵中对行做操作和对列做操作花费的时间。
```
n = 10000;
mtrx = rand(n,n);
mcol = zeros(n,1);
mrow = zeros(1,n);
tic
for i=1:n
    mcol(i) = sum(mtrx(:,i));
end
toc
```
我们对矩阵中的每一列都求和，总共用了0.17秒。

```
tic
for i=1:n
    mrow(i) = sum(mtrx(i,:));
end
toc
```
再对矩阵中的每一行求和，用了0.8秒。  
可以看出，对列操作比对行操作速度要快很多。

4.数据类型

&emsp;&emsp;在Matlab中，数据类型默认是double型，对使用者来说，无需太多关心数据类型当然是省心省力的，但这也带来了一个问题就是double型占用的内存较多，还有可能拖慢程序的运行速度。所以，在适当的情况下，我们可以把数据类型选择为逻辑型、字符型、整型等。但这样还需要注意的一点是，一个变量在改变数据类型时会消耗额外的时间，因此还不如重新建一个新变量。   
&emsp;&emsp;高效编程的内容就先写这么多，后面还会继续补充。下面说一个Matlab调试中断点设置问题。在一个for循环中，比如`for i=1:n`，我们想在`i=100`的进入断点，这个时候应该怎么用？以前的时候我们都会这么写
```
for i=1:n
    if(i==100)
        pass
    end
end
```
&emsp;&emsp;把断点设置在`pass`处，但其实不用这么弄。Matlab中提供了条件断点的设置方式。在循环中右键选择`设置条件断点`，如下图所示。



<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/matlab/debug/debug.png">
</p>



在下面的窗口中填入条件即可，比如`i==100`。


<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/matlab/debug/debug2.png">
</p>


这样，当程序运行到`i==100`时就会进入断点，不需要自己再写额外的语句。


