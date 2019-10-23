---
youtube_id: kiCkWF3rsl0
qq_video_id: 
b_av: 73006468
b_cid: 124802496
b_page: 1
title: FPGA时序约束理论篇之建立保持时间
description: "建立时间 保持时间理论"
chapter: 1
category: timing-analysis
post-headings:
author-link: #
no-video: false
publish-date: 2019-10-22
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 1
---

 
 

# 读万卷书--时序约束理论篇           
           
## 周期约束理论

&emsp;&emsp;首先来看什么是时序约束，泛泛来说，就是我们告诉软件（Vivado、ISE等）从哪个pin输入信号，输入信号要延迟多长时间，时钟周期是多少，让软件PAR(Place and  Route)后的电路能够满足我们的要求。因此如果我们不加时序约束，软件是无法得知我们的时钟周期是多少，PAR后的结果是不会提示时序警告的。

&emsp;&emsp;周期约束就是告诉软件我们的时钟周期是多少，让它PAR后要保证在这样的时钟周期内时序不违规。大多数的约束都是周期约束，因为时序约束约的最多是时钟。

&emsp;&emsp;在讲具体的时序约束前，我们先介绍两个概念，在下面的讲解中，会多次用到：
 - 发起端/发起寄存器/发起时钟/发起沿：指的是产生数据的源端
 - 接收端/接收寄存器/捕获时钟/捕获沿：指的是接收数据的目的端

### 建立/保持时间

&emsp;&emsp;讲时序约束，这两个概念要首先介绍，因为我们做时序约束其实就是为了满足建立/保持时间。对于DFF的输入而言，
 - 在clk上升沿到来之前，数据提前一个最小时间量“预先准备好”，这个最小时间量就是建立时间；
 - 在clk上升沿来之后，数据必须保持一个最小时间量“不能变化”，这个最小时间量就是保持时间。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial0.png"  alt="" />

</center>

建立和保持时间是由器件特性决定了，当我们决定了使用哪个FPGA，就意味着建立和保持时间也就确定了。Xilinx FPGA的setup time基本都在0.04ns的量级，hold time基本在0.2ns的量级，不同器件会有所差异，具体可以查对应器件的DC and AC Switching Characteristics，下图列出K7系列的建立保持时间。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial1.png"  alt="" />

</center>






