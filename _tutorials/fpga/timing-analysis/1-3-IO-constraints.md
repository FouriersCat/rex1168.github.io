---
youtube_id: y9jbF9whZJE
qq_video_id: 
b_av: 73006468
b_cid: 124803022
b_page: 3
title: FPGA时序约束理论篇之IO约束
description: "IO理论"
chapter: 1
category: FPGA-Timing-Constraints
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 3
---



## I/O约束

&emsp;&emsp;I/O约束是必须要用的约束，又包括管脚约束和延迟约束。

### 管脚约束

&emsp;&emsp;管脚约束就是指管脚分配，我们要指定管脚的PACKAGE_PIN和IOSTANDARD两个属性的值,前者指定了管脚的位置,后者指定了管脚对应的电平标准。

&emsp;&emsp;在vivado中，使用如下方式在xdc中对管脚进行约束。
```
set_property -dict {PACKAGE_PIN AJ16  IOSTANDARD  LVCMOS18} [get_ports "led[0]"    ]
```
&emsp;&emsp;在Vivado规定，必须要指定管脚电平，不然在最后一步生成比特流时会出错。

&emsp;&emsp;除了管脚位置和电平，还有一个大家容易忽略但很容易引起错误的就是端接，当我们使用差分电平时比如LVDS，在在V6中我们使用`IBUFDS`来处理输入的差分信号时，可以指定端接为TRUE。
```
   IBUFDS #(
      .DIFF_TERM("TRUE"),       // Differential Termination
      .IOSTANDARD("DEFAULT")     // Specify the input I/O standard
   ) IBUFDS_inst (
      .O(O),  // Buffer output
      .I(I),  // Diff_p buffer input (connect directly to top-level port)
      .IB(IB) // Diff_n buffer input (connect directly to top-level port)
   );
```

但在Ultrascale中的IBUFDS，却把端接这个选项去掉了
```

   IBUFDS #(
      .DQS_BIAS("FALSE")  // (FALSE, TRUE)
   )
   IBUFDS_inst (
      .O(O),   // 1-bit output: Buffer output
      .I(I),   // 1-bit input: Diff_p buffer input (connect directly to top-level port)
      .IB(IB)  // 1-bit input: Diff_n buffer input (connect directly to top-level port)
   );
```
我们必须要在xdc或I/O Pors界面中，手动指定，否则可能会出错。

<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial6.png)

</center>

笔者之前就采过一个坑，差分端口输入，当连续输入的数据为11101111这种时，中间那个0拉不下来，还是1，同样也会发生在000010000，这样就导致数据传输错误，后来才发现是端接忘记加。因为端接会影响信号的实际电平，导致FPGA判断错误。

&emsp;&emsp;当综合完成后，我们可以点击DRC，进行设计规则检查，这一步可以报出一些关键问题，比如时钟端口未分配在时钟引脚上等。

<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial7.png)

</center>




### 延迟约束

&emsp;&emsp;延迟约束用的是`set_input_delay`和`set_output_delay`，分别用于input端和output端，其时钟源可以是时钟输入管脚，也可以是虚拟时钟。**但需要注意的是，这个两个约束并不是起延迟的作用**，具体原因下面分析。

 - set_input_delay
 
&emsp;&emsp;这个约束跟ISE中的`OFFSET=IN`功能相同，但设置方式不同。下图所示即为input delay的约束说明图。

<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial8.png)

</center>

从图中很容易理解，
```math
T\_inputdelay = Tco + TD
```

当满足图中的时序时，最大延迟为2ns，最小延迟为1ns。

因此，需要加的时序约束为：
```
create_clock -name sysclk -period 10 [get_ports clkin]
set_input_delay 2 -max -clock sysclk [get_ports Datain]
set_input_delay 1 -min -clock sysclk [get_ports Datain]
```



 - set_output_delay

&emsp;&emsp;set_output_delay的用法跟set_input_delay十分相似，这里就不再展开讲了。我们上面讲set_input_delay的描述中，大家可以看到，这个约束是告诉vivado我们的输入信号和输入时钟之间的延迟关系，跟下面要讲的时钟周期约束是一个原理，让vivado在这个前提下去Place and Route。**并不是调节输入信号的延迟**，因为身边有不少的FPGA工程师在没用过这个约束指令之前，都以为这是调节延迟的约束。

&emsp;&emsp;如果要调整输入信号的延迟，只能使用IDELAY，在V6中，IDELAY模块有32个tap值，每个tap可延迟78ps，这样总共差不多是2.5ns。

