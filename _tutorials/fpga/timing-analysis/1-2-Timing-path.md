---
youtube_id: y9jbF9whZJE
qq_video_id: 
b_av: 73006468
b_cid: 124802760
b_page: 2
title: FPGA时序约束理论篇之时序路径与时序模型
description: "时序路径和时序模型理论"
chapter: 1
category: FPGA-Timing-Constraints
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 2
---


### 时序路径

&emsp;&emsp;典型的时序路径有4类，如下图所示，这4类路径可分为片间路径（标记①和标记③)和片内路径（标记②和标记④）。


<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial2.png)

</center>

&emsp;&emsp;对于所有的时序路径，我们都要明确其起点和终点，这4类时序路径的起点和终点分别如下表。
<center>

时序路径                              |        起点   | 终点     | 应用约束
--------------------------------------|---------------| -------- | -----
①输入端口到FPGA内部第一级触发器的路径 | ChipA/clk     | rega/D   | set_input_delay
②FPGA内部触发器之间的路径             | rega/clk      | regb/D   | create_clock
③FPGA内部末级触发器到输出端口的路径   | regb/clk      | ChipB/D  | set_output_delay
④FPGA输入端口到输出端口的路径         | 输入端口      | 输出端口 | set_max_delay

</center>

&emsp;&emsp;这4类路径中，我们最为关心是②的同步时序路径，也就是FPGA内部的时序逻辑。



### 时序模型

&emsp;&emsp;典型的时序模型如下图所示，一个完整的时序路径包括源时钟路径、数据路径和目的时钟路径，也可以表示为触发器+组合逻辑+触发器的模型。

<center>

<img src="https://note.youdao.com/yws/public/resource/7d47e4aa0853ace5ba365e775fb69177/xmlnote/4913712BD65E450CBA43C8E01051E042/13350" width = "600" height = "350" alt="" />


</center>

&emsp;&emsp;该时序模型的要求为(公式1)

```math
Tclk ≥ Tco + Tlogic + Trouting + Tsetup - Tskew  
```



其中，Tco为发端寄存器时钟到输出时间；Tlogic为组合逻辑延迟；Trouting为两级寄存器之间的布线延迟；Tsetup为收端寄存器建立时间；Tskew为两级寄存器的时钟歪斜，其值等于时钟同边沿到达两个寄存器时钟端口的时间差；Tclk为系统所能达到的最小时钟周期。

&emsp;&emsp;这里我们多说一下这个Tskew，skew分为两种，positive skew和negative skew，其中positive skew见下图，这相当于增加了后一级寄存器的触发时间。

<center>

<img src="https://note.youdao.com/yws/public/resource/7d47e4aa0853ace5ba365e775fb69177/xmlnote/CD066808B64746DA8EF1E80BC1F57D8F/13298" width = "500" height = "300" alt="" />

</center>

但对于negative skew，则相当于减少了后一级寄存器的触发时间，如下图所示。

<center>

<img src="https://note.youdao.com/yws/public/resource/7d47e4aa0853ace5ba365e775fb69177/xmlnote/B812000FC8D447F59C5059BA79B4DBA6/13334" width = "500" height = "300" alt="" />

</center>

&emsp;&emsp;当系统稳定后，都会是positive skew的状态，但即便是positive skew，综合工具在计算时序时，也不会把多出来的Tskew算进去。


&emsp;&emsp;用下面这个图来表示时序关系就更加容易理解了。为什么要减去Tskew，下面这个图也更加直观。

<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial3.png)


</center>

发送端寄存器产生的数据，数据经过Tco、Tlogic、Trouting后到达接收端，同时还要给接收端留出Tsetup的时间。而时钟延迟了Tskew的时间，因此有：
（公式2）
```math
Tdata\_path + Tsetup <= Tskew + Tclk 
```

对于同步设计Tskew可忽略(认为其值为0)，因为FPGA中的时钟树会尽量保证到每个寄存器的延迟相同。

公式中提到了建立时间，那保持时间在什么地方体现呢？

&emsp;&emsp;保持时间比较难理解，它的意思是reg1的输出不能太快到达reg2，这是为了防止采到的新数据太快而冲掉了原来的数据。保持时间约束的是同一个时钟边沿，而不是对下一个时钟边沿的约束。


<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial4.png)
</center>


&emsp;&emsp;reg2在边沿2时刻刚刚捕获reg1在边沿1时刻发出的数据，若reg1在边沿2时刻发出的数据过快到达reg2，则会冲掉前面的数据。因此保持时间约束的是同一个边沿。
<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial5.png)

</center>

在时钟沿到达之后，数据要保持Thold的时间，因此，要满足：（公式3）

```math
Tdata\_path =  Tco + Tlogic + Trouting ≥ Tskew + Thold
```

&emsp;&emsp;这两个公式是FPGA的面试和笔试中经常问到的问题，因为这种问题能反映出应聘者对时序的理解。



&emsp;&emsp;在公式1中，Tco跟Tsu一样，也取决于芯片工艺，因此，一旦芯片型号选定就只能通过Tlogic和Trouting来改善Tclk。其中，Tlogic和代码风格有很大关系，Trouting和布局布线的策略有很大关系。


&emsp;&emsp;关于时序约束的基本理论就讲这么多，下面讲具体的约束。

