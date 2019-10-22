---
youtube_id: y9jbF9whZJE
qq_video_id: 
b_av: 73006468
b_cid: 124800314
b_page: 8
title: FPGA时序约束实战篇之主时钟约束
description: ""
chapter: 2
category: FPGA-Timing-Constraints
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 8
---


## 约束主时钟

&emsp;&emsp;在这一节开讲之前，我们先把`wave_gen`工程的`wave_gen_timing.xdc`中的内容都删掉，即先看下在没有任何时序约束的情况下会综合出什么结果？

对工程综合并Implementation后，Open Implemented Design，会看到下图所示内容。


<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial40.png)
</center>

&emsp;&emsp;可以看到，时序并未收敛。可能到这里有的同学就会有疑问，我们都已经把时序约束的内容都删了，按我们第一讲中提到的“因此如果我们不加时序约束，软件是无法得知我们的时钟周期是多少，PAR后的结果是不会提示时序警告的”，这是因为在该工程中，用了一个MMCM，并在里面设置了输入信号频率，因此这个时钟软件会自动加上约束。


&emsp;&emsp;接下来，我们在tcl命令行中输入`report_clock_networks -name main`，显示如下：

<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial41.png)
</center>

&emsp;&emsp;可以看出，Vivado会自动设别出两个主时钟，其中clk_pin_p是200MHz，这个是直接输入到了MMCM中，因此会自动约束；另一个输入时钟clk_in2没有约束，需要我们手动进行约束。

或者可以使用`check_timing -override_defaults no_clock`指令，这个指令我们之前的内容讲过，这里不再重复讲了。

&emsp;&emsp;在tcl中输入
```
create_clock -name clk2 -period 25 [get_ports clk_in2]
```
注：在Vivado中，可以直接通过tcl直接运行时序约束脚本，运行后Vivado会自动把这些约束加入到xdc文件中。

再执行`report_clock_networks -name main`，显示如下：
<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial42.png)
</center>

可以看到，主时钟都已被正确约束。

