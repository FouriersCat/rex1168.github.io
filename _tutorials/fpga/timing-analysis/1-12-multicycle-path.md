---
youtube_id: I60g0cCpZsM
qq_video_id: 
b_av: 73006468
b_cid: 124801729
b_page: 12
title: FPGA时序约束实战篇之多周期路径约束
description: ""
chapter: 2
category: timing-analysis
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 12
---



## 多周期路径约束

&emsp;&emsp;多周期路径，我们一般按照以下4个步骤来约束：
 1. 带有使能的数据
 
&emsp;&emsp;首先来看带有使能的数据，在本工程中的Tming Report中，也提示了同一个时钟域之间的几个路径建立时间不满足要求

<center>

<img src="https://github.com/Bounce00/pic/blob/master/fpga/timing_toturial63.png?raw=true"  alt="" />

</center>

&emsp;&emsp;其实这几个路径都是带有使能的路径，使能的周期为2倍的时钟周期，本来就应该在2个时钟周期内去判断时序收敛。因此，我们添加时序约束：
```
set_multicycle_path 2 -setup -from [get_cells {cmd_parse_i0/send_resp_data_reg[*]} -include_replicated_objects] -to [get_cells {resp_gen_i0/to_bcd_i0/bcd_out_reg[*]}]
set_multicycle_path 1 -hold -from [get_cells {cmd_parse_i0/send_resp_data_reg[*]} -include_replicated_objects] -to [get_cells {resp_gen_i0/to_bcd_i0/bcd_out_reg[*]}]
```

也可以写为：
```
set_multicycle_path -from [get_cells {cmd_parse_i0/send_resp_data_reg[*]} -include_replicated_objects] -to [get_cells {resp_gen_i0/to_bcd_i0/bcd_out_reg[*]}] 2
set_multicycle_path -hold -from [get_cells {cmd_parse_i0/send_resp_data_reg[*]} -include_replicated_objects] -to [get_cells {resp_gen_i0/to_bcd_i0/bcd_out_reg[*]}] 1
```

这两种写法是等价的。


我们也可以直接点击右键通过GUI的方式进行约束，效果都是一样的。



&emsp;&emsp;在工程的`uart_tx_ctl.v`和`uart_rx_ctl.v`文件中，也存在带有使能的数据，但这些路径在未加多路径约束时并未报出时序错误或者警告。

在接收端，捕获时钟频率是200MHz，串口速率是115200，采用16倍的Oversampling，因此使能信号周期是时钟周期的200e6/115200/16=108.5倍。


在接收端，捕获时钟频率是166667MHz，串口速率是115200，采用16倍的Oversampling，因此使能信号周期是时钟周期的166.667e6/115200/16=90.4倍。

&emsp;&emsp;因此，时序约束如下：
```
# 串口接收端
set_multicycle_path  -from [get_cells uart_rx_i0/uart_rx_ctl_i0/* -filter IS_SEQUENTIAL] -to [get_cells uart_rx_i0/uart_rx_ctl_i0/* -filter IS_SEQUENTIAL] 108
set_multicycle_path -hold -from [get_cells uart_rx_i0/uart_rx_ctl_i0/* -filter IS_SEQUENTIAL] -to [get_cells uart_rx_i0/uart_rx_ctl_i0/* -filter IS_SEQUENTIAL] 107
# 串口发送端
set_multicycle_path -from [get_cells uart_tx_i0/uart_tx_ctl_i0/* -filter IS_SEQUENTIAL] -to [get_cells uart_tx_i0/uart_tx_ctl_i0/* -filter IS_SEQUENTIAL] 90
set_multicycle_path -hold -from [get_cells uart_tx_i0/uart_tx_ctl_i0/* -filter IS_SEQUENTIAL] -to [get_cells uart_tx_i0/uart_tx_ctl_i0/* -filter IS_SEQUENTIAL] 89
```

约束中的`filter`参数也将在下一章节具体讲解。

 2. 两个有数据交互的时钟之间存在相位差

&emsp;&emsp;在本工程中，没有这种应用场景，因此不需要添加此类约束。



 3. 存在快时钟到慢时钟的路径

&emsp;&emsp;在本工程中，没有这种应用场景，因此不需要添加此类约束。



 4. 存在慢时钟到快时钟的路径

&emsp;&emsp;在本工程中，没有这种应用场景，因此不需要添加此类约束。


综上，我们所有的时序约束如下：
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

# 伪路径约束
set_false_path -from [get_clocks clk_rx] -to [get_clocks clk_tx]
set_false_path -from [get_ports rst_pin]

# 多周期约束
set_multicycle_path 2 -setup -from [get_cells {cmd_parse_i0/send_resp_data_reg[*]} -include_replicated_objects] -to [get_cells {resp_gen_i0/to_bcd_i0/bcd_out_reg[*]}]
set_multicycle_path 1 -hold -from [get_cells {cmd_parse_i0/send_resp_data_reg[*]} -include_replicated_objects] -to [get_cells {resp_gen_i0/to_bcd_i0/bcd_out_reg[*]}]

# 串口接收端
set_multicycle_path 108 -setup -from [get_cells uart_rx_i0/uart_rx_ctl_i0/* -filter IS_SEQUENTIAL] -to [get_cells uart_rx_i0/uart_rx_ctl_i0/* -filter IS_SEQUENTIAL]
set_multicycle_path 107 -hold -from [get_cells uart_rx_i0/uart_rx_ctl_i0/* -filter IS_SEQUENTIAL] -to [get_cells uart_rx_i0/uart_rx_ctl_i0/* -filter IS_SEQUENTIAL]
# 串口发送端
set_multicycle_path 90 -setup -from [get_cells uart_tx_i0/uart_tx_ctl_i0/* -filter IS_SEQUENTIAL] -to [get_cells uart_tx_i0/uart_tx_ctl_i0/* -filter IS_SEQUENTIAL] 
set_multicycle_path 89 -hold -from [get_cells uart_tx_i0/uart_tx_ctl_i0/* -filter IS_SEQUENTIAL] -to [get_cells uart_tx_i0/uart_tx_ctl_i0/* -filter IS_SEQUENTIAL]
```

&emsp;&emsp;重新Synthesis并Implementation后，可以看到，已经没有了时序错误

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial49.png"  alt="" />

</center>


&emsp;&emsp;仅有的两个warning也只是说rst没有设置input_delay，spi_clk_pin没有设置output_delay，但我们已经对rst设置了伪路径，而spi_clk_pin是我们约束的输出时钟，无需设置output_delay。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial49.png"  alt="" />

</center>

&emsp;&emsp;到这里，教科书版的时序约束教程就基本讲完了。但我们平时的工程中，跟上面这种约束还是有差异的：

 1. 首先是虚拟时钟，这个约束在平时的工程中基本不会用到，像需要设置虚拟时钟的场景，我们也都是通过设计来保证时序收敛，设置虚拟时钟的意义不大。    
 2. 第二就是output delay，在FPGA的最后一级寄存器到输出的路径上，往往都使用了IOB，也就是IO block，因此最后一级寄存器的位置是固定的，从buffer到pad的走线延时是确定的。在这种情况下，是否满足时序要求完全取决于设计，做约束只是验证一下看看时序是否收敛。所以也基本不做。但是input delay是需要的，因为这是上一级器件输出的时序关系。
 3. 第三个就是多周期路径，我们讲了那么多多周期路径的应用场景，但实际我们是根据Timing report来进行约束的，即便那几种场景都存在，但如果Timing report中没有提示任何的时序 warning，我们往往也不会去添加约束。

 4. 第四个就是在设置了多周期后，如果还是提示Intra-Clocks Paths的setup time不过，那就要看下程序，是否写的不规范。比如



&emsp;&emsp;如果设置了多周期路径后，还是提示`Intra-Clocks Paths`的`setup time`不过，那就要看下程序，是否写的不规范。比如
```
always @ (posedge clk)
begin
    regA <= regB;
    
    if(regA != regB)
        regC <= 4'hf;
    else 
        regC <= {regC[2:0], 1'b0};
    
    if((&flag[3:0]) && regA != regB) 
        regD <= regB;
end
```
这么写的话，如果时钟频率稍微高一点，比如250MHz，就很容易导致从regB到regD的setup time不满足要求。因为begin end里面的代码都是按顺序执行的，要在4ns内完成这些赋值与判断的逻辑，挑战还是挺大的。因此，我们可以改写为：
```
always @ (posedge clk)
begin
    regA <= regB;
end 

always @ (posedge clk)
begin
    if(regA != regB)
        regC <= 4'hf;
    else 
        regC <= {regC[2:0], 1'b0};
end 

always @ (posedge phy_clk)
begin
    if((&flag[3:0]) && regA != regB) 
        regD <= regB;
end 
```
把寄存器的赋值分开，功能还是一样的，只是分到了几个always中，这样就不会导致时序问题了。



