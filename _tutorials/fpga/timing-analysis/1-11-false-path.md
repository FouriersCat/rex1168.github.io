---
youtube_id: y9jbF9whZJE
qq_video_id: 
b_av: 73006468
b_cid: 124801250
b_page: 11
title: FPGA时序约束实战篇之伪路径约束
description: ""
chapter: 2
category: FPGA-Timing-Constraints
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 11
---


## 伪路径约束

&emsp;&emsp;在不加伪路径的时序约束时，Timing Report会提示很多的error，其中就有跨时钟域的error。



<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial47.png)
</center>

我们可以直接在上面右键，然后设置两个时钟的伪路径。

<center>

![image](https://github.com/Bounce00/pic/blob/master/fpga/timing_toturial62.png?raw=true)
</center>

这样会在xdc中自动生成如下约束：
```
set_false_path -from [get_clocks -of_objects [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKOUT0]] -to [get_clocks -of_objects [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKOUT1]]
```

&emsp;&emsp;其实这两个时钟我们已经在前面通过generated指令创建过了，因此get_pins那一长串就没必要重复写了，所以我们可以手动添加这两个时钟的伪路径如下：
```
set_false_path -from [get_clocks clk_rx] -to [get_clocks clk_tx]
```
伪路径的设置是单向的，如果两个时钟直接存在相互的数据的传输，则还需要添加从`clk_tx`到`clk_rx`的路径，这个工程中只有从rx到tx的数据传输，因此这一条就可以了。

这里再修改一条第7讲中的错误，第7讲中时钟树的图里，只有从`clk_rx`到`clk_tx`的箭头，不应该有从`clk_tx`到`clk_rx`的箭头。

&emsp;&emsp;在伪路径一节中，我们讲到过异步复位也需要添加伪路径，`rst_pin`的复位输入在本工程中就是当做异步复位使用，因此还需要添加一句：
```
set_false_path -from [get_ports rst_pin]
```

&emsp;&emsp;对于`clk_samp`和`clk2`，它们之间存在数据交换，但我们在前面已经约束过`asynchronous`了，这里就可以不用重复约束了。

&emsp;&emsp;这里需要提示一点，添加了上面这些约束后，综合时会提示xdc文件的的warning。
<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial48.png)
</center>

但这可能是Vivado的综合过程中，读取到该约束文件时，内部电路并未全都建好，就出现了没有发现`clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKIN1`等端口的情况，有如下几点证明：
 1. 这个端口在代码中确实是存在的
 2. 若把该xdc文件，设置为仅在Implementation中使用，则不会提示该warning
 3. 在Implementation完成后，无论是Timing Report还是通过tcl的`report_clocks`指令，都可以看到这几个时钟已经被正确约束。下图所示即为设置完上面的约束后的Timing Report。
 


<center>

![image](https://github.com/Bounce00/pic/blob/master/fpga/timing_toturial61.png?raw=true)

</center>
