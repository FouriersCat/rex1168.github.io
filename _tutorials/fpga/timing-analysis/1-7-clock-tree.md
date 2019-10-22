---
youtube_id: y9jbF9whZJE
qq_video_id: 
b_av: 73006468
b_cid: 124799973
b_page: 7
title: FPGA时序约束实战篇之梳理时钟树
description: "梳理时钟树"
chapter: 2
category: FPGA-Timing-Constraints
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 7
---


# 行万里路--时序约束实战篇


&emsp;&emsp;我们以Vivado自带的`wave_gen`工程为例，该工程的各个模块功能较为明确，如下图所示。为了引入异步时钟域，我们在此程序上由增加了另一个时钟--`clkin2`，该时钟产生脉冲信号`pulse`，`samp_gen`中在`pulse`为高时才产生信号。

<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial37.png)
</center>


下面我们来一步一步进行时序约束。

## 梳理时钟树

&emsp;&emsp;我们首先要做的就是梳理时钟树，就是工程中用到了哪些时钟，各个时钟之间的关系又是什么样的，如果自己都没有把时钟关系理清楚，不要指望综合工具会把所有问题暴露出来。

&emsp;&emsp;在我们这个工程中，有两个主时钟，四个衍生时钟，如下图所示。

<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial38.png)
</center>

&emsp;&emsp;确定了主时钟和衍生时钟后，再看各个时钟是否有交互，即clka产生的数据是否在clkb的时钟域中被使用。

&emsp;&emsp;这个工程比较简单，只有两组时钟之间有交互，即：
 - `clk_rx`与`clk_tx`
 - `clk_samp`与`clk2`

其中，`clk_rx`和`clk_tx`都是从同一个MMCM输出的，两个频率虽然不同，但他们却是同步的时钟，因此他们都是从同一个时钟分频得到（可以在Clock Wizard的Port Renaming中看到VCO Freq的大小），因此它们之间需要用`set_false_path`来约束；而`clk_samp`和`clk2`是两个异步时钟，需要用`asynchronous`来约束。


<center>

![image](https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial39.png)
</center>

完成以上两步，就可以进行具体的时钟约束操作了。