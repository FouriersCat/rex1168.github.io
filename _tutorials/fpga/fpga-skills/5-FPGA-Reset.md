---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: FPGA复位的正确打开方式
description: "."
chapter: 1
category: fpga-skills
post-headings:
author-link: #
no-video: true
publish-date: 2020-05-30
thumbnail: /static/img/course_cover-small/fpga-skills.png
index: 5
---


 



&emsp;&emsp;本篇文章参考Xilinx White Paper：    

**Get Smart About Reset: Think Local, Not Global**


在没看这篇文章前，回想一下平时我们常用的复位方式：    

&emsp;&emsp;1.首先，上电后肯定是要复位一下，不然仿真时会出现没有初值的情况；    
&emsp;&emsp;2.最好有个复位的按键，在调试时按一下复位键就可以全局复位了；    
&emsp;&emsp;3.也许是同步复位，也许是异步复位，不同的工程师可能有不同的方案。

但如果认真看了Xilinx的White Paper，就会对复位有了新的认识。我们把White Paper的内容总结为下面4个问题：    

&emsp;&emsp;1.需不需要复位？    

&emsp;&emsp;2.同步复位 or 异步复位？  

&emsp;&emsp;3.高复位 or 低复位？

&emsp;&emsp;4.全局复位 or 局部复位？怎么用？

### 1. 需不需要复位？

&emsp;&emsp;看到这个问题，可能很多同学会有点懵，怎么可能不需要复位？其实Xilinx FPGA在系统上电配置时，会有一个GSR(Global Set/Reset)的信号，这个信号有以下几个特点：
 - 预布线
 - 高扇出
 - 可靠的


这个信号可初始化所有的cell，包括所有的Flip-Flop和BRAM。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531130406701.png)




如果我们在程序里用自己生成的复位信号，也只能复位Flip-Flop。

&emsp;&emsp;这个GSR信号我们可以在程序中通过实例化`STARTUP`直接调用，但Xilinx并不推荐这么使用。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531130607103.png)

主要原因是FPGA会把像系统复位这种高扇出的信号放到高速布线资源上，这比使用GSR要快，而且更容易进行时序分析。


&emsp;&emsp;虽然有GSR，但这并不是说要避免使用复位信号，以下两种情况就必须要加复位：
 - 带有反馈的模块，比如IIR这种滤波器和状态机，当状态跑飞了，就需要复位一下

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020053113063255.png)

 - 应用过程中需要复位的寄存器
 
&emsp;&emsp;这个就具体看是什么应用了，我们公司的很多寄存器都需要在调试过程中需要经常复位，像这种复位就是必须的了。

所以，需不需要复位完全看设计。这里多提一点，时序收敛也是一样，主要看设计，而不是约束。


### 2.同步复位 or 异步复位？  

&emsp;&emsp;在HDL中，如果敏感列表中不包含rst，会被综合成同步复位：
```
always @ ( posedge clk )
begin
    if(rst)
        ...
end
```

如果敏感列表中包含rst，则会被综合成异步复位：
```
always @ ( posedge clk or posedge rst)
begin
    ...
end
```




同步复位的好处，不言而喻，有利于时序分析，降低亚稳态的几率，避免毛刺。

同步信号的缺点：
 - 复位信号有效电平持续时间必须大于时钟周期，不然时钟可能采不到复位
 - 在没有时钟的时候无法复位

&emsp;&emsp;也有很多同学会说同步复位会需要额外的资源，但对于Xilinx的FPGA，是没有这个问题的，具体原因后面讲。


对于异步复位，好处就是同步复位的反方面：脉冲宽度没有限制，没有时钟也可以复位。


缺点就是异步电路，容易引起亚稳态，产生毛刺，不利于时序分析，而且不同触发器的复位时间可能不同。下面这个图中，在A时刻接收到复位信号拉低的FF可以在下一个时钟上升沿时就释放复位状态，但C时刻接收到复位信号拉低的FF则在下下个时钟上升沿时才能释放复位状态。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531130702323.png)


按照White Paper上所讲，99.99%的概率这种情况都不会发生，但如果你刚好碰到一次这种现象，那你就是那0.01%。

下面我们来举一个例子来说明同步复位和异步复位，FPGA为V7，代码如下：

```
module rst_demo(
 input clk,
 input rst1,
 input rst2,
 input in1,
 input in2,
 output reg out1,
 output reg out2);
 
 always @ ( posedge clk )
 begin
    if(rst1)
        out1 <= 1'b0;
    else
        out1 <= in1;
 end
 
 always @ ( posedge clk or posedge rst2 )
 begin
    if(rst2)
        out2 <= 1'b0;
    else
        out2 <= in2;
 end
 
 endmodule
```

综合后的schematic如下图：


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531130733112.png)


可以看出来，同步复位和异步复位都是占用一个Storage Element，我们在之前的一篇文章中讲过Storage Element可以配置为Latch，同样的，也可以配置为FDRE和FDCE，而且在7Series手册中也并未提到配置成FDRE或FDCE时是否会占用更多资源（比如，7Series的FPGA中，一个Slice中有8个Storage Element，如果其中一个被配置成了Latch，那有4个Storage Element是不能用的），因此在Xilinx的FPGA中，同步复位和异步复位在资源占用上，并没有区别。



### 3.高复位 or 低复位？

&emsp;&emsp;很多处理器上的复位都是低复位，这也导致了很多同学在使用复位信号时也习惯使用低复位了。但从我们上一节所讲中可以看出，无论是同步复位还是异步复位，复位信号都是高有效，如果采用低复位，还需要增加一个反相器。

&emsp;&emsp;如果接收到其他处理器发过来的低有效复位信号，我们最好在顶层模块中翻转复位信号的极性，这样做可以将反相器放入IO Logic中，不会占用FPGA内部的逻辑资源和布线资源。

&emsp;&emsp;这里多补充一点，如果使用Zynq和Microblaze，则Reset模块默认是低复位，我们可以手动设置为高复位。


### 4.全局复位 or 局部复位？怎么用？

&emsp;&emsp;我们对复位常用的做法是将系统中的每个FF都连接到某个复位信号，但这样就造成了复位信号的高扇出，高扇出就容易导致时序的违规。而且全局复位占用的资源比我们想象中要高的多：
 - 布线资源占用
   - 其他网络的布线空间就相应减少
   - 可能会降低系统性能
   - 增加布线时间
 - 逻辑资源占用
   - 占用FF作为专门的复位电路
   - 如果该复位信号还受其他信号控制，会导致FF的输入前增加门电路
   - 会增加整个设计的size
   - 增加的逻辑资源会影响系统性能
   - 增加布局布线时间
 - 全局复位不会使用像SRL16E这种高效结构
   - 在LUT中SRL16E可当作16个FF
   - 这些Virtual FF不支持复位
   - 增加设计的size，并降低系统性能
   - 增加布局布线时间


因此，Xilinx推荐尽量使用局部复位的方式，前面我们也讲到然同步复位和异步复位都多多少少有些问题，那有没有一种方式可以结合同步复位和异步复位的优点？当然有，就是异步复位，同步释放。这种方法可以将两者结合起来，取长补短。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531130758818.png)

图中的Verilog代码如下：

```
module rst_demo(
 input      clk, 
 input      rst_async, 
 (* keep = "true" *)
 output  reg  rst_module1 = 0,
 (* keep = "true" *)
 output  reg  rst_module2 = 0
    );

reg         sys_rst;
reg         rst_r;

always @(posedge clk or posedge rst_async) begin
    if (rst_async) begin
        rst_r <= 1'b1;
    end
    else begin
        rst_r <= 1'b0;
    end
end

always @(posedge clk or posedge rst_async) begin
    if (rst_async) begin
        sys_rst <= 1'b1;
    end
    else begin
        sys_rst <= rst_r;
    end
end

always @ ( posedge clk ) begin
    rst_module1 <= sys_rst;
    rst_module2 <= sys_rst;
end

endmodule
```

<div align=center>

综合后的schematic如下图：

</div>


![pic1](https://img-blog.csdnimg.cn/20200531131251546.png)



异步复位模块输出的sys_rst通过n个D触发器后输出给n个模块，当做模块的复位信号

### 总结

&emsp;&emsp;在使用复位信号时，考虑这个寄存器需不需要在运行过程中进行复位，如果只需要上电后复位一次，那只需在定义时写上初值即可，无需使用其他复位信号；在Xilinx的FPGA中尽量使用高有效的复位信号，采用异步复位同步释放的方式，并且要将复位信号局部化，避免使用高扇出的全局复位。


微信公众号：Quant_Times

要进FPGA技术交流群的朋友可以加微信：xhclsys2























