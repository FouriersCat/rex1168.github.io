---
youtube_id: rp6xn4uPOM4
qq_video_id: 
b_av: 73006468
b_cid: 124803254
b_page: 4
title: FPGA时序约束理论篇之时钟周期约束
description: "时钟周期约束理论"
chapter: 1
category: timing-analysis
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 4
---



## 时钟周期约束

&emsp;&emsp;时钟周期约束，顾名思义，就是我们对时钟的周期进行约束，这个约束是我们用的最多的约束了，也是最重要的约束。

&emsp;&emsp;下面我们讲一些Vivado中时钟约束指令。

 **1. Create_clock**

&emsp;&emsp;在Vivado中使用`create_clock`来创建时钟周期约束。使用方法为：
```
create_clock -name <name> -period <period> -waveform {<rise_time> <fall_time>} [get_ports <input_port>]
```


参数       | 含义
--------   | ----
-name      | 时钟名称
-period    | 时钟周期，单位为ns
-waveform  | 波形参数，第一个参数为时钟的第一个上升沿时刻，第二个参数为时钟的第一个下降沿时刻
-add       | 在同一时刻源上定义多个时钟时使用

&emsp;&emsp;这里的时钟必须是主时钟`primary clock`，**主时钟**通常有两种情形:一种是时钟由外部时钟源提供，通过时钟引脚进入FPGA，该时钟引脚绑定的时钟为主时钟:另一种是高速收发器(GT)的时钟RXOUTCLK或TXOUTCLK。对于7系列FPGA，需要对GT的这两个时钟手工约束：对于UltraScale FPGA，只需对GT的输入时钟约束即可，Vivado会自动对这两个时钟约束。



&emsp;&emsp;如何确定主时钟是时钟周期约束的关键，除了根据主时钟的两种情形判断之外，还可以借助Tcl脚本判断。

&emsp;&emsp;在vivado自带的example project里面，打开CPU(HDL)的工程，如下图所示。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial10.png"  alt="" />

</center>

把工程的xdc文件中，`create_clock`的几项都注释掉。这里解释下端口（Port）和管脚（Pin）。get_ports获取的是FPGA的IO端口，get_pins获取的是FPGA内部子模块的Pin，具体的我们在第14讲的Tcl命令中会讲到。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial11.png"  alt="" />

</center>

再`Open Synthesized Design`或者`Open Implementation Design`，并通过以下两种方式查看主时钟。

 - 方式一
 
运行tcl指令`report_clock_networks -name mainclock`，显示结果如下：

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial12.png"  alt="" />

</center>

 - 方式二
运行tcl指令`check_timing -override_defaults no_clock`，显示结果如下：

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial13.png"  alt="" />

</center>

***Vivado中的tcl命令行相当好用，有很多的功能，大家可以开始习惯用起来了。***

&emsp;&emsp;对于高速收发器的时钟，我们也以Vivado中的CPU example工程为例，看下Xilinx官方是怎么约束的。


```
# Define the clocks for the GTX blocks
create_clock -name gt0_txusrclk_i -period 12.8 [get_pins mgtEngine/ROCKETIO_WRAPPER_TILE_i/gt0_ROCKETIO_WRAPPER_TILE_i/gtxe2_i/TXOUTCLK]
create_clock -name gt2_txusrclk_i -period 12.8 [get_pins mgtEngine/ROCKETIO_WRAPPER_TILE_i/gt2_ROCKETIO_WRAPPER_TILE_i/gtxe2_i/TXOUTCLK]
create_clock -name gt4_txusrclk_i -period 12.8 [get_pins mgtEngine/ROCKETIO_WRAPPER_TILE_i/gt4_ROCKETIO_WRAPPER_TILE_i/gtxe2_i/TXOUTCLK]
create_clock -name gt6_txusrclk_i -period 12.8 [get_pins mgtEngine/ROCKETIO_WRAPPER_TILE_i/gt6_ROCKETIO_WRAPPER_TILE_i/gtxe2_i/TXOUTCLK]
```






&emsp;&emsp;当系统中有多个主时钟，且这几个主时钟之间存在确定的相位关系时，需要用到`-waveform`参数。如果有两个主时钟，如下图所示。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial14.png"  alt="" />

</center>


则时钟约束为：
```
create_clock -name clk0 -period 10.0 -waveform {0 5} [get_ports clk0]
create_clock -name clk1 -period 8.0 -waveform {2 8} [get_ports clk1]
```

约束中的数字的单位默认是ns，若不写`wavefrom`参数，则默认是占空比为50%且第一个上升沿出现在0时刻。使用`report_clocks`指令可以查看约束是否生效。还是上面的CPU的例子，把约束都还原到最初的状态。执行`report_clocks`后，如下所示，我们只列出其中几项内容。

```
Clock Report

Clock           Period(ns)  Waveform(ns)    Attributes  Sources
sysClk          10.000      {0.000 5.000}   P           {sysClk}
gt0_txusrclk_i  12.800      {0.000 6.400}   P           {mgtEngine/ROCKETIO_WRAPPER_TILE_i/gt0_ROCKETIO_WRAPPER_TILE_i/gtxe2_i/TXOUTCLK}
...


====================================================
Generated Clocks
====================================================

Generated Clock   : clkfbout
Master Source     : clkgen/mmcm_adv_inst/CLKIN1
Master Clock      : sysClk
Multiply By       : 1
Generated Sources : {clkgen/mmcm_adv_inst/CLKFBOUT}

Generated Clock   : cpuClk_4
Master Source     : clkgen/mmcm_adv_inst/CLKIN1
Master Clock      : sysClk
Edges             : {1 2 3}
Edge Shifts(ns)   : {0.000 5.000 10.000}
Generated Sources : {clkgen/mmcm_adv_inst/CLKOUT0}
...

```


&emsp;&emsp;一般来讲，我们的输入时钟都是差分的，此时我们只对P端进行约束即可。如果同时约束了P端和N端，通过`report_clock_interaction`命令可以看到提示unsafe。这样既会增加内存开销，也会延长编译时间。

 **2. create_generated_clock**
 
其使用方法为：
```
create_generated_clock -name <generated_clock_name> \
                       -source <master_clock_source_pin_or_port> \
                       -multiply_by <mult_factor> \
                       -divide_by <div_factor> \
                       -master_clock <master_clk> \
                       <pin_or_port>

```


参数         | 含义
-----------  | ----
-name        | 时钟名称
-source      | 产生该时钟的源时钟
-multiply_by | 源时钟的多少倍频
-divide_by   | 源时钟的多少分频



&emsp;&emsp;从名字就能看出来，这个是约束我们在FPGA内部产生的衍生时钟， 所以参数在中有个`-source`，就是指定这个时钟是从哪里来的，这个时钟叫做`master clock`，是指上级时钟，区别于`primary clock`。
它可以是我们上面讲的primary clock，也可以是其他的衍生时钟。该命令不是设定周期或波形，而是描述时钟电路如何对上级时钟进行转换。这种转换可以是下面的关系：

 - 简单的频率分频
 - 简单的频率倍频
 - 频率倍频与分频的组合，获得一个非整数的比例，通常由MMCM或PLL完成
 - 相移或波形反相
 - 占空比改变
 - 上述所有关系的组合

<br />

&emsp;&emsp;**衍生时钟**又分两种情况：
 1. Vivado自动推导的衍生时钟
 2. 用户自定义的衍生时钟

&emsp;&emsp;首先来看第一种，如果使用PLL或者MMCM，则Vivado会自动推导出一个约束。大家可以打开Vivado中有个叫`wavegen`的工程，在这个工程中，输入时钟经过PLL输出了2个时钟，如下图所示。
（补充：关于DCM/DLL/PLL/MMCM的区别，可参考我写的另一篇文章[DCM/DLL/PLL/MMCM区别](https://mp.weixin.qq.com/s?__biz=MzU4ODY5ODU5Ng==&mid=2247484106&idx=1&sn=82983a8086732717298436e067a64d4d&chksm=fdd98441caae0d57c99c5b22cf72bfaee2372824406014680be9df1f8d85b3071182fe43656c&mpshare=1&scene=1&srcid=0928ySJ3ud0vfaGS85Teu5Xw&sharer_sharetime=1571051171309&sharer_shareid=296cfe717a7da125d89d5a7bcdf65c18&key=6234e09828e71f223a5bbb62942587523cffdc550c50d6713403e50f0f1a03c87c5b1a6fae054a425e6f27eabfd6e48eb8fd421c5841d8d8b3b054113d8e8650ff4a65e51fa211ebe10dc0a436635167&ascene=1&uin=MzkzMzM2Nzc1&devicetype=Windows+7&version=62070141&lang=zh_CN&pass_ticket=b15xLCDB%2FBp7ALLPd%2FcqR3Z4qIXNCYrUowj5c4g9AzKzb29vj6R%2F%2BP8z8RJvomTk)

（在微信公证号Quant_Times中搜索就能看到）

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial15.png"  alt="" />

</center>


&emsp;&emsp;但在xdc文件中，并未对这2个输出时钟进行约束，只对输入的时钟进行了约束，若我们使用`report_clocks`指令，则会看到：

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial16.png"  alt="" />

</center>

*注：有三个约束是因为PLL会自动输出一个反馈时钟*

&emsp;&emsp;自动推导的好处在于当MMCM/PLL/BUFR的配置改变而影响到输出时钟的频率和相位时，用户无需改写约束，Vivado仍然可以自动推导出正确的频率/相位信息。劣势在于，用户并不清楚自动推导出的衍生钟的名字，当设计层次改变时，衍生钟的名字也有可能改变。但由于该衍生时钟的约束并非我们自定义的，因此可能会没有关注到它名字的改变，当我们使用者这些衍生时钟进行别的约束时，就会出现错误。


&emsp;&emsp;解决办法是用户自己手动写出自动推导的衍生时钟的名字，也仅仅写出名字即可，其余的不写。如下所示。

```
create_generated_clock -name <generated_clock_name> \
                       -source <master_clock_source_pin_or_port>
```

这一步很容易会被提示critical warning，其实有个很简单的方法，就是name和source都按照vivado中生成的来。具体我们到后面的例子中会讲到。


 **3. set_clock_groups**
 
 使用方法为：
 ```
 set_clock_groups -asynchronous -group <clock_name_1> -group <clock_name_2>
 set_clock_groups -physically_exclusive  -group <clock_name_1> -group <clock_name_2>
```

&emsp;&emsp;这个约束常用的方法有三种，第一种用法是当两个主时钟是异步关系时，使用`asynchronous`来指定。这个在我们平时用的还是比较多的，一般稍微大点的工程，都会出现至少两个主时钟，而且这两个时钟之间并没有任何的相位关系，这时就要指定：

```
create_clock -period 10 -name clk1 [get_ports clk1]
create_clock -period 8 -name clk2 [get_ports clk2]
set_clock_groups -asynchronous -group clk1 -group clk2
```

&emsp;&emsp;第二种用法是当我们需要验证同一个时钟端口在不同时钟频率下能否获得时序收敛时使用。比如有两个异步主时钟clk1和clk2，需要验证在clk2频率为100MHz，clk1频率分别为50MHz、100MHz和200MHz下的时序收敛情况，我们就可以这样写。
```
create_clock -name clk1A -period 20.0 [get_ports clk1]
create_clock -name clk1B -period 10.0 [get_ports clk1] -add
create_clock -name clk1C -period 5.0  [get_ports clk1] -add 
create_clock -name clk2 -period 10.0 [get_ports clk2]
set_clock_groups -physically_exclusive -group clk1A -group clk1B -group clk1C
set_clock_groups -asynchronous -group "clk1A clk1B clk1C" -group clk2
```
&emsp;&emsp;第三种用法就是当我们使用BUFGMUX时，会有两个输入时钟，但只会有一个时钟被使用。比如MMCM输入100MHz时钟，两个输出分别为50MHz和200MHz，这两个时钟进入了BUFGMUX，如下图所示。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial17.png"  alt="" />
</center>

在这种情况下，我们需要设置的时序约束如下：
```
set_clock_groups -logically_exclusive \
-group [get_clocks -of [get_pins inst_mmcm/inst/mmcm_adv_inst/CLKOUT0]] \
-group [get_clocks -of [get_pins inst_mmcm/inst/mmcm_adv_inst/CLKOUT1]]
```


 **4. 创建虚拟时钟**
 
&emsp;&emsp;虚拟时钟通常用于设定对输入和输出的延迟约束，这个约束其实是属于IO约束中的延迟约束，之所以放到这里来讲，是因为虚拟时钟的创建，用到了本章节讲的一些理论。虚拟时钟和前面讲的延迟约束的使用场景不太相同。顾名思义，虚拟时钟，就是没有与之绑定的物理管脚。
虚拟时钟主要用于以下三个场景：
 - 外部IO的参考时钟并不是设计中的时钟
 - FPGA I/O路径参考时钟来源于内部衍生时钟，但与主时钟的频率关系并不是整数倍
 - 针对I/O指定不同的jitter和latency

 
&emsp;&emsp;简而言之，之所以要创建虚拟时钟，对于输入来说，是因为输入到FPGA数据的捕获时钟是FPGA内部产生的，与主时钟频率不同；或者PCB上有Clock Buffer导致时钟延迟不同。对于输出来说，下游器件只接收到FPGA发送过去的数据，并没有随路时钟，用自己内部的时钟去捕获数据。



&emsp;&emsp;如下图所示，在FPGA的A和B端口分别有两个输入，其中捕获A端口的时钟是主时钟，而捕获B端口的时钟是MMCM输出的衍生时钟，而且该衍生时钟与主时钟的频率不是整数倍关系。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial18.png"  alt="" />

</center>


&emsp;&emsp;这种情况下时序约束如下：
```
create_clock -name sysclk -period 10 [get_ports clkin]
create_clock -name virclk -period 6.4
set_input_delay 2 -clock sysclk [get_ports A]
set_input_delay 2 -clock virclk [get_ports B]
```
可以看到，创建虚拟时钟用的也是`create_clock`约束，但后面并没有加`get_ports`参数，因此被称为虚拟时钟。

&emsp;&emsp;再举个输出的例子，我们常用的UART和SPI，当FPGA通过串口向下游器件发送数据时，仅仅发过去了uart_tx这个数据，下游器件通过自己内部的时钟去捕获uart_tx上的数据，这就需要通过虚拟时钟来约束；而当FPGA通过SPI向下游器件发送数据时，会发送sclk/sda/csn三个信号，其中sclk就是sda的随路时钟，下游器件通过sclk去捕获sda的数据，而不是用自己内部的时钟，这是就不需要虚拟时钟，直接使用`set_output_delay`即可。


注意，虚拟时钟必须在约束I/O延迟之前被定义。


 5. 最大最小延迟约束
 
&emsp;&emsp;顾名思义，就是设置路径的max/min delay，主要应用场景有两个：
 - 输入管脚的信号经过组合逻辑后直接输出到管脚
 - 异步电路之间的最大最小延迟

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial19.png"  alt="" />

</center>

设置方式为：
```
set_max_delay <delay> [-datapath_only] [-from <node_list>][-to <node_list>][-through <node_list>]
set_min_delay <delay> [-from <node_list>] [-to <node_list>][-through <node_list>]
```

参数       | 含义
--------   | ----
-from      | 有效的起始节点包含:时钟,input(input)端口,或时序单元(寄存器,RAM)的时钟引脚.
-to    | 有效的终止节点包含:时钟,output(inout)端口或时序单元的数据端口.
-through  | 有效的节点包含:引脚,端口,线网.

&emsp;&emsp;max/min delay的约束平时用的相对少一些，因为在跨异步时钟域时，我们往往会设置`asynchronous`或者`false_path`。对于异步时钟，我们一般都会通过设计来保证时序能够收敛，而不是通过时序约束来保证。


