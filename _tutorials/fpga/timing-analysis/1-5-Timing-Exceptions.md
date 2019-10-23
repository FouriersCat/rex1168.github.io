---
youtube_id: ifvS9LtNI8s
qq_video_id: 
b_av: 73006468
b_cid: 124804340
b_page: 5
title: FPGA时序约束理论篇之两种时序例外
description: "伪路径约束和多周期路径约束理论"
chapter: 1
category: timing-analysis
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 5
---


## 两种时序例外


### 多周期路径


&emsp;&emsp;上面我们讲的是时钟周期约束，默认按照单周期关系来分析数据路径，即数据的发起沿和捕获沿是最邻近的一对时钟沿。如下图所示。


<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial20.png"  alt="" />

</center>

&emsp;&emsp;默认情况下,保持时间的检查是以建立时间的检查为前提,即总是在建立时间的前一个时钟周期确定保持时间检查。这个也不难理解，上面的图中，数据在时刻1的边沿被发起，建立时间的检查是在时刻2进行，而保持时间的检查是在时刻1（如果这里不能理解，再回头看我们讲保持时间章节的内容），因此保持时间的检查是在建立时间检查的前一个时钟沿。


&emsp;&emsp;但在实际的工程中，经常会碰到数据被发起后，由于路径过长或者逻辑延迟过长要经过多个时钟周期才能到达捕获寄存器；又或者在数据发起的几个周期后，后续逻辑才能使用。这时如果按照单周期路径进行时序检查，就会报出时序违规。因此就需要我们这一节所讲的多周期路径了。

多周期约束的语句是：
```
set_multicycle_path <num_cycles> [-setup|-hold] [-start|-end][-from <startpoints>] [-to <endpoints>] [-through <pins|cells|nets>]
```


参数       | 含义
------------------------------   | ----
num_cycles [-setup  -hold]      | 建立/保持时间的周期个数
[-start  -end]                  | 参数时钟选取
-from <startpoint>               | 发起点
-to <endpoint>                   | 捕获点
-through <pins/cells/nets>       | 经过点


对于建立时间，num_cycles是指多周期路径所需的时钟周期个数；对于保持时间，num_cycles是指相对于默认的捕获沿，实际捕获沿应回调的周期个数。


发起沿和捕获沿可能是同一个时钟，也可能是两个时钟，参数`start`和`end`就是选择参考时钟是发送端还是接收端。


 - start表示参考时钟为发送端（发端）所用时钟，对于保持时间的分析，若后面没有指定`start`或`end`，则默认为为-start；
 - end表示参考时钟为捕获端（收端）所用时钟,对于建立时间的分析，若后面没有指定`start`或`end`，则默认为为-end；

上面这两句话也不难理解，因为setup-time是在下一个时钟沿进行捕获时的约束，因此默认是对接收端的约束；而hold-up-time是对同一个时钟沿的约束，目的是发送端不能太快，是对发送端的约束。

&emsp;&emsp;对于单周期路径来说，setup的num_cycles为1，hold的num_cycles为0.


&emsp;&emsp;多周期路径要分以下几种情况进行分析：

**1. 单时钟域**

&emsp;&emsp;即发起时钟和捕获时钟是同一个时钟，其多周期路径模型如下图所示。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial21.png"  alt="" />

</center>

&emsp;&emsp;单时钟域的多周期路径常见于带有使能的电路中，我们以双时钟周期路径为例，其实现电路如下：

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial22.png"  alt="" />

</center>

&emsp;&emsp;若我们没有指定任何的约束，默认的建立/保持时间的分析就像我们上面所讲的单周期路径，如下图所示。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial23.png"  alt="" />

</center>

&emsp;&emsp;但由于我们的的数据经过了两个时钟周期才被捕获，因此建立时间的分析时需要再延迟一个周期的时间。

采用如下的时序约束：
```
set_multicycle_path 2 -setup -from [get_pins data0_reg/C] -to [get_pins data1_reg/D]
```

在建立时间被修改后，保持时间也会自动调整到捕获时钟沿的前一个时钟沿，如下图所示。
<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial24.png"  alt="" />

</center>

很明显，这个保持时间检查是不对的，因为保持时间的检查针对的是同一个时钟沿，因此我们要把保持时间往回调一个周期，需要再增加一句约束：
```
set_multicycle_path 1 -hold -end -from [get_pins data0_reg/C]  -to [get_pins data1_reg/D]
```

这里加上`-end`参数是因为我们要把捕获时钟沿往前移，因此针对的是接收端，但由于我们这边讲的是单时钟域，发送端和接收端的时钟是同一个，因此`-end`可以省略。这样，完整的时序约束如下：
```
set_multicycle_path 2 -setup -from [get_pins data0_reg/C] -to [get_pins data1_reg/D]
set_multicycle_path 1 -hold  -from [get_pins data0_reg/C]  -to [get_pins data1_reg/D]
```

约束完成后，建立保持时间检查如下图所示。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial25.png"  alt="" />
</center>

&emsp;&emsp;在单时钟域下，若数据经过N个周期到达，则约束示例如下：
```
set_multicycle_path N -setup -from [get_pins data0_reg/C] -to [get_pins data1_reg/D]
set_multicycle_path N-1 -hold  -from [get_pins data0_reg/C]  -to [get_pins data1_reg/D]
```


**2. 时钟相移**

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial26.png"  alt="" />

</center>

&emsp;&emsp;前面我们讨论的是在单时钟域下，发送端和接收端时钟是同频同相的，如果两个时钟同频不同相怎么处理？

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial27.png"  alt="" />

</center>

&emsp;&emsp;如上图所示，时钟周期为4ns，接收端的时钟沿比发送端晚了0.3ns，若不进行约束，建立时间只有0.3ns，时序基本不可能收敛；而保持时间则为3.7ns，过于丰富。可能有的同学对保持时间会有疑惑，3.7ns是怎么来的？还记得我们上面讲的保持时间的定义么，在0ns时刻，接收端捕获到发送的数据后，要再过3.7ns的时间发送端才会发出下一个数据，因此本次捕获的数据最短可持续3.7ns，即保持时间为3.7ns。

&emsp;&emsp;因此，在这种情况下，我们应把捕获沿向后移一个周期，约束如下：
```
set_multicycle_path 2 -setup -from [get_clocks CLK1] -to [get_clocks CLK2]
```
对setup约束后，hold会自动向后移动一个周期，此时的建立保持时间检查如下：

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial28.png"  alt="" />
</center>

&emsp;&emsp;那如果接收端的时钟比发送端的时钟超前了怎么处理？

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial29.png"  alt="" />
</center>

&emsp;&emsp;同样的，时钟周期为4ns，但接收端时钟超前了0.3ns，从图中可以看出，此时setup是3.7ns，而保持时间是0.3ns。这两个时间基本已经满足了Xilinx器件的要求，因此无需进行约束。


**3. 慢时钟到快时钟的多周期**

&emsp;&emsp;当发起时钟慢于捕获时钟时，我们应该如何处理？

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial30.png"  alt="" />
</center>


&emsp;&emsp;假设捕获时钟频率是发起时钟频率的3倍，在没有任何约束的情况下，Vivado默认会按照如下图所示的建立保持时间进行分析。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial31.png"  alt="" />
</center>


&emsp;&emsp;但我们可以通过约束让建立时间的要求更容易满足，即
```
set_multicycle_path 3 -setup -from [get_clocks CLK1] -to [get_clocks CLK2]
```
跟上面讲的一样，设置了setup，hold会自动变化，但我们不希望hold变化，因此再增加：
```
set_multicycle_path 2 -hold -end -from [get_clocks CLK1] -to [get_clocks CLK2]
```

这里由于发起和捕获是两个时钟，因此`-end`参数是不可省的。加上时序约束后，Vivado会按照下面的方式进行时序分析。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial32.png"  alt="" />
</center>






**4. 快时钟到慢时钟的多周期**

&emsp;&emsp;当发起时钟快于捕获时钟时，我们应该如何处理？

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial33.png"  alt="" />
</center>

&emsp;&emsp;假设发起时钟频率是捕获时钟频率的3倍，在没有任何约束的情况下，Vivado默认会按照如下图所示的建立保持时间进行分析。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial34.png"  alt="" />
</center>

&emsp;&emsp;同理，我们可以通过约束，让时序条件更加宽裕。
```
set_multicycle_path 3 -setup -start -from [get_clocks CLK1] -to [get_clocks CLK2]
set_multicycle_path 2 -hold -from [get_clocks CLK1] -to [get_clocks CLK2]
```
这里的hold约束中没有加`-end`参数，这样的话默认就是`-start`，是因为我们把发起时钟回调2个周期，如下图所示。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial35.png"  alt="" />
</center>

针对上面讲的几种多周期路径，总结如下：

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial36.png"  alt="" />
</center>



### 伪路径

&emsp;&emsp;什么是伪路径？伪路径指的是该路径存在，但该路径的电路功能不会发生或者无须时序约束。如果路径上的电路不会发生，那Vivado综合后会自动优化掉，因此我们无需考虑这种情况。

&emsp;&emsp;为什么要创建伪路径？创建伪路径可以减少工具运行优化时间,增强实现结果,避免在不需要进行时序约束的地方花较多时间而忽略了真正需要进行优化的地方。

&emsp;&emsp;伪路径一般用于：
 - 跨时钟域
 - 一上电就被写入数据的寄存器
 - 异步复位或测试逻辑
 - 异步双端口RAM

&emsp;&emsp;可以看出，伪路径主要就是用在异步时钟的处理上，我们上一节讲的多周期路径中，也存在跨时钟域的情况的，但上面我们讲的是两个同步的时钟域。

伪路径的约束为：
```
set_false_path [-setup] [-hold] [-from <node_list>] [-to <node_list>] [-through <node_list>]
```

 - `-from`的节点应是有效的起始点.有效的起始点包含时钟对象,时序单元的clock引脚,或者input(or inout)原语;

 - `-to`的节点应包含有效的终结点.一个有效的终结点包含时钟对象,output(or inout)原语端口,或者时序功能单元的数据输入端口;

 - `-through`的节点应包括引脚,端口,或线网.当单独使用-through时,应注意所有路径中包含-through节点的路径都将被时序分析工具所忽略.

需要注意的是，`-through`是有先后顺序的，下面的两个约束是不同的约束：
```
set_false_path -through cell1/pin1 -through cell2/pin2
set_false_path -through cell2/pin2 -through cell1/pin1
```
因为它们经过的先后顺序不同，伪路径的约束是单向的，并非双向的，若两个时钟域相互之间都有数据传输，则应采用如下约束：
```
set_false_path -from [get_clocks clk1] -to [get_clocks clk2]
set_false_path -from [get_clocks clk2] -to [get_clocks clk1]
```
也可以直接采用如下的方式，与上述两行约束等效：
```
set_clock_groups -async -group [get_clocks clk1] -to [get_clocks clk2]
```

&emsp;&emsp;还有一些其他的约束，比如case analysis、disabling timing和bus_skew等，由于平时用的比较少，这里就不讲了。
