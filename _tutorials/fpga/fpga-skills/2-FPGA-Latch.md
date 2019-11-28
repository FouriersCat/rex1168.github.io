---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: 大家一致避免使用的锁存器为什么依然存在于FPGA中？我们对锁存器有什么误解？
description: "."
chapter: 1
category: fpga-skills
post-headings:
author-link: #
no-video: true
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-skills.png
index: 2
---


 
 

### 前言

&emsp;&emsp;在FPGA的设计中，避免使用锁存器是几乎所有FPGA工程师的共识，Xilinx和Altera也在手册中提示大家要慎用锁存器，除非你明确知道你确实需要一个latch来解决问题。而且目前网上大多数文章都对锁存器有个误解，我们后面会详细说明。

&emsp;&emsp;这篇文章，我们包含如下内容：
 1. 锁存器、触发器和寄存器的原理和区别，为什么锁存器不好？
 2. 什么样的代码会产生锁存器？
 3. 为什么锁存器依然存在于FPGA中？


### 锁存器、触发器和寄存器的原理和区别，为什么锁存器不好？


&emsp;&emsp;锁存器、触发器和寄存器它们的英文分别为：Latch、Flip-Flop、Register。我们对这三个单词的翻译真的是非常直观，从名字就能大概猜出它们的含义。


#### 锁存器

1. 什么是锁存器？

&emsp;&emsp;锁存器就是用来存储状态信息，就是将这个状态一直保持。锁存器对脉冲的电平敏感，也就是电平触发，在有效的电平下，锁存器处于使能状态，输出随着输入发生变化，此时它不锁存信号，就像一个缓冲器一样；在锁存器没有使能时，则数据被锁住，输入信号不起作用，此时输出一直为锁存的状态信息。我们常见的锁存器有SR锁存器、D锁存器、JK锁存器等。

2. 锁存器的工作过程

&emsp;&emsp;我们以最简单的D锁存器为例来说明锁存器的工作过程，D锁存器有3个接口，也可以认为是4个，因为两个输出只是单纯的反向关系。


<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/latch1.png">
</p>



&emsp;&emsp;其中D为输入信号，当E为高时，输出Q即为输入的D；当E为低时，Q保持E为高时的最后一次状态，也就是锁存过程。


<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/latch2.png">
</p>



3. 为什么锁存器不好？

&emsp;&emsp;从上面的图中可以看出，锁存器对毛刺不敏感，很容易在信号上产生毛刺；而且也没有时钟信号，不容易进行静态时序分析。正是因为这两个原因，我们在FPGA设计时，尽量不用锁存器。

&emsp;&emsp;当然，目前网上还有一种说法是FPGA中只有LUT和FF的资源，没有现成的Latch，所以如果要用Latch，需要更多的资源来搭出来。但这一观点，是错误的，我们后面会有专门的讲解。



#### 触发器

1. 什么是触发器
 

&emsp;&emsp;触发器（Flip-Flop，简写为 FF），也叫双稳态门，又称双稳态触发器。在中国台湾及中国香港译作“正反器”，是一种具有两种稳态的用于储存的组件，可记录二进制数字信号“1”和“0”。

&emsp;&emsp;FPGA工程师，对触发器再熟悉不过了，D触发器应该是我们平时写程序中用到最多的element。除了D触发器，常见的触发器还有T触发器、SR触发器、JK触发器等。触发器对脉冲边沿敏感，其状态只在时钟脉冲的上升沿或下降沿的瞬间改变。



2. 触发器的工作过程

&emsp;&emsp;我们以D触发器为例来说明触发器的工作过程，D触发器接口如下：

<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/latch3.png">
</p>

&emsp;&emsp;触发器只在时钟边沿时起作用，所以哪怕输入的信号中有毛刺，输出还是比较干净的。

<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/latch4.png">
</p>


&emsp;&emsp;还有一点需要了解的是，FPGA中最小的单元是门电路，门电路又组成了锁存器，锁存器组成了寄存器。

#### 寄存器

&emsp;&emsp;用来存放数据的一些小型存储区域，用来暂时存放参与运算的数据和运算结果，它被广泛的用于各类数字系统和计算机中。其实寄存器就是一种常用的时序逻辑电路，但这种时序逻辑电路只包含存储电路。寄存器的存储电路是由锁存器或触发器构成的，因为一个锁存器或触发器能存储1位二进制数，所以由N个锁存器或触发器可以构成N位寄存器。 工程中的寄存器一般按计算机中字节的位数设计，所以一般有8位寄存器、16位寄存器等。


### 什么样的代码会产生锁存器？

**在组合逻辑中**，如果条件描述不全就会容易产生Latch：
1. if语句中缺少了else语句
2. case语句中没有给出全部的情况。

也就是下面的情况：
```
always @ *
begin
    if(en==1)
        q <= d;
end
```

```
input [1:0]d;
always @ (d)
begin
    case(d)
    0:       q0 <= 1'b1;
    1:       q2 <= 1'b1;
    2:       q2 <= 1'b1;
    3:       q3 <= 1'b1;
    default: q4 <= 1'b1;
end
```

&emsp;&emsp;这个前提是组合电路中，在时序电路的if语句中，及时没有else，也不会综合出Latch的。

&emsp;&emsp;上面这两种写法容易出现在什么地方呢？最常见的就是状态机，我见过不少的FPGA工程师在写状态机时，case语句中没有给出变量的全部情况。



### 为什么锁存器依然存在于FPGA中？

&emsp;&emsp;我们在前面说过网上有一种说法是：FPGA中只有LUT和FF的资源，没有现成的Latch，所以如果要用Latch，需要更多的资源来搭出来。这种说法是错误的，因为在Xilinx的FPGA中，6 系列之前的器件中都有Latch；6系列和7系列的FPGA中，一个Slice中有50%的storage element可以被配置为Latch或者Flip-Flop，另外一半只能被配置为Flip-Flop。比如7系列FPGA中，一个Slice中有8个Flip-Flop，如果被配置成了Latch，该Slice的另外4个Flip-Flop就不能用了。这样确实造成了资源的浪费。

<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/latch5.png">
</p>

在UltraScale的FPGA中，所有的storage element都可以被配置成Flip-Flop和Latch。

<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/latch6.png">
</p>

我们以下面的代码来说明Flip-Flop和Latch在Ultrascale的FPGA中Implementation后的结果。

Flip-Flop代码：
```
module FF_top(
 input              clk,
 input [3:0]        data_i,
 input              data_ie,  //enable
 output reg [3:0]   o_latch
    );
    
always @ ( posedge clk )
begin
    if(data_ie)
        o_latch <= data_i;
end  
    
endmodule
```

Latch代码：
```
module latch_top(
 input [7:0]        data_i,
 input              data_ie,  //enable
 output reg [7:0]   o_latch
    );
    
always @ * 
begin
    if(data_ie)
        o_latch[3:0] <= data_i[3:0];
end  


endmodule
```

Flip-Flop实现后的Schematic和Device如下：

<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/latch7.png">
</p>



Latch实现后的Schematic和Device如下：

<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/latch8.png">
</p>

可以看出，在使用Flip-Flop时，storage element被综合成了FDRE，也就是触发器；当使用Latch电路时，storage element被综合成了LDCE。

&emsp;&emsp;所以，FPGA中没有Latch的说法在Xilinx的FPGA中是不对的。


&emsp;&emsp;**最后一个问题，既然Latch有这么多的问题，那为什么FPGA中还要保留？**

1. 首先就是因为FPGA电路的灵活性，保留Latch并不影响FPGA的资源，因为storage element可以直接被配置为Flip-Flop。
2. 其次就是有些功能是必须要使用Latch的，比如很多处理器的接口就需要一个Latch来缓存数据或地址。


&emsp;&emsp;最后要说明的一点是：锁存器虽然在FPGA中不怎么被使用，但在CPU中却很常见，因为锁存器比Flip-Flop快很多。








