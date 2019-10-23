---
youtube_id: EMZgn8v2DGQ
qq_video_id: 
b_av: 73006468
b_cid: 124800959
b_page: 10
title: FPGA时序约束实战篇之延迟约束
description: ""
chapter: 2
category: timing-analysis
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 10
---



## 延迟约束

&emsp;&emsp;对于延迟约束，相信很多同学是不怎么用的，主要可能就是不熟悉这个约束，也有的是嫌麻烦，因为有时还要计算PCB上的走线延迟导致的时间差。而且不加延迟约束，Vivado也只是在Timing Report中提示warning，并不会导致时序错误，这也会让很多同学误以为这个约束可有可无。
<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial45.png"  alt="" />
</center>



&emsp;&emsp;但其实这种想法是不对的，比如在很多ADC的设计中，输出的时钟的边沿刚好是数据的中心位置，而如果我们不加延迟约束，则Vivado会默认时钟和数据是对齐的。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial46.png"  alt="" />
</center>

&emsp;&emsp;对于输入管脚，首先判断捕获时钟是主时钟还是衍生时钟，如果是主时钟，直接用`set_input_delay`即可，如果是衍生时钟，要先创建虚拟时钟，然后再设置delay。对于输出管脚，判断有没有输出随路时钟，若有，则直接使用`set_output_delay`，若没有，则需要创建虚拟时钟。

&emsp;&emsp;在本工程中，输入输出数据管脚的捕获时钟如下表所示：


管脚       |   输入输出 | 捕获时钟  | 时钟类型    | 是否有随路时钟 | 是否需要虚拟时钟
-----------|------------|---------  | ------------|--------------  | -------------
rxd_pin    | 输入       | clk_pin_p | 主时钟      | x              | No
txd_pin    | 输出       | clk_tx    | x           | 无             | Yes
lb_sel_pin | 输入       | clk_tx    | 衍生时钟    | x              | Yes
led_pins[7:0]| 输出     | clk_tx    | 衍生时钟    | 无             | Yes
spi_mosi_pin | 输出     | spi_clk   | 衍生时钟    | 有             | No
dac_*     |  输出       | spi_clk   | 衍生时钟    | 有             | No

&emsp;&emsp;根据上表，我们创建的延迟约束如下，其中的具体数字在实际工程中要根据上下游器件的时序关系（在各个器件手册中可以找到）和PCB走线延迟来决定。未避免有些约束有歧义，我们把前面的所有约束也加进来。
```
# 主时钟约束
create_clock -period 25.000 -name clk2 [get_ports clk_in2]

# 衍生时钟约束
create_generated_clock -name clk_samp -source [get_pins clk_gen_i0/clk_core_i0/clk_tx] -divide_by 32 [get_pins clk_gen_i0/BUFHCE_clk_samp_i0/O]
create_generated_clock -name spi_clk -source [get_pins dac_spi_i0/out_ddr_flop_spi_clk_i0/ODDR_inst/C] -divide_by 1 -invert [get_ports spi_clk_pin]
create_generated_clock -name clk_tx -source [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKIN1] [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKOUT1]
create_generated_clock -name clk_rx -source [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKIN1] [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKOUT0]

# 设置异步时钟
set_clock_groups -asynchronous -group [get_clocks clk_samp] -group [get_clocks clk2]

# 延迟约束
create_clock -period 6.000 -name virtual_clock
set_input_delay -clock [get_clocks -of_objects [get_ports clk_pin_p]] 0.000 [get_ports rxd_pin]
set_input_delay -clock [get_clocks -of_objects [get_ports clk_pin_p]] -min -0.500 [get_ports rxd_pin]
set_input_delay -clock virtual_clock -max 0.000 [get_ports lb_sel_pin]
set_input_delay -clock virtual_clock -min -0.500 [get_ports lb_sel_pin]
set_output_delay -clock virtual_clock -max 0.000 [get_ports {txd_pin {led_pins[*]}}]
set_output_delay -clock virtual_clock -min -0.500 [get_ports {txd_pin {led_pins[*]}}]
set_output_delay -clock spi_clk -max 1.000 [get_ports {spi_mosi_pin dac_cs_n_pin dac_clr_n_pin}]
set_output_delay -clock spi_clk -min -1.000 [get_ports {spi_mosi_pin dac_cs_n_pin dac_clr_n_pin}]
```
