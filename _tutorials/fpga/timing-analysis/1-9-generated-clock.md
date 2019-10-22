---
youtube_id: y9jbF9whZJE
qq_video_id: 
b_av: 73006468
b_cid: 124800584
b_page: 9
title: FPGA时序约束实战篇之衍生时钟约束
description: ""
chapter: 2
category: FPGA-Timing-Constraints
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 9
---



## 约束衍生时钟

&emsp;&emsp;系统中有4个衍生时钟，但其中有两个是MMCM输出的，不需要我们手动约束，因此我们只需要对`clk_samp`和`spi_clk`进行约束即可。约束如下：
```
create_generated_clock -name clk_samp -source [get_pins clk_gen_i0/clk_core_i0/clk_tx] -divide_by 32 [get_pins clk_gen_i0/BUFHCE_clk_samp_i0/O]
create_generated_clock -name spi_clk -source [get_pins dac_spi_i0/out_ddr_flop_spi_clk_i0/ODDR_inst/C] -divide_by 1 -invert [get_ports spi_clk_pin]
```


&emsp;&emsp;我们再运行`report_clocks`，显示如下：
<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial43.png)
</center>

我们在理论篇的“create_generated_clock”一节中讲到，我们可以重新设置Vivado自动生成的衍生时钟的名字，这样可以更方便我们后续的使用。按照前文所讲，只需设置`name`和`source`参数即可，其中这个`source`可以直接从`report_clocks`中得到，因此我们的约束如下：
```
create_generated_clock -name clk_tx -source [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKIN1] [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKOUT1]
create_generated_clock -name clk_rx -source [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKIN1] [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKOUT0]
```
&emsp;&emsp;大家可以对比下`report_clocks`的内容和约束指令，很容易就能看出它们之间的关系。

把上述的约束指令在tcl中运行后，我们再运行一遍`report_clocks`，显示如下：
<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial44.png)
</center>

在时序树的分析中，我们看到，`clk_samp`和`clk2`两个异步时钟之间存在数据交互，因此要进行约束，如下：
```
set_clock_groups -asynchronous -group [get_clocks clk_samp] -group [get_clocks clk2]
```
