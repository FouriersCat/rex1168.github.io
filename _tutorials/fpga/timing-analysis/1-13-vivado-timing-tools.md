---
youtube_id: fNOKyJO19Fs
qq_video_id: 
b_av: 73006468
b_cid: 124802329
b_page: 13
title: Vivado时序约束辅助工具
description: ""
chapter: 3
category: timing-analysis
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 13
---


# 时序约束辅助工具

&emsp;&emsp;上面我们讲的都是xdc文件的方式进行时序约束，Vivado中还提供了两种图形界面的方式，帮我们进行时序约束：时序约束编辑器（Edit Timing Constraints ）和时序约束向导（Constraints Wizard）。两者都可以在综合或实现后的Design中打开。

## 1. 时序约束编辑器

&emsp;&emsp;打开之后就可显示出我们之前做的所有约束，当然，还可以再添加、删除或修改时序约束。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial51.png"  alt="" />
</center>
    
&emsp;&emsp;比如我们要新添加一个主时钟，先选中左边的`Create Clock`，再点击`+`号添加约束，然后就会看到下面的界面，按下图中步骤操作。
    
<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial52.png"  alt="" />
</center>

其中，选择时钟按钮会弹出一个新的窗口，如下图所示，我们只需根据时钟名字进行查找并选择即可。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial53.png"  alt="" />
</center>

## 2. 时序约束向导

&emsp;&emsp;时序约束向导可以自动识别出未约束的主时钟，我们把wave_gen工程的xdc文件中对clk2的时钟约束注释掉，重新综合并实现后，打开时序约束向导，可以看到clk2被检测出未约束，点击编辑按钮，设置参数后就可完成约束。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial54.png"  alt="" />
</center>

&emsp;&emsp;时序约束向导会按照主时钟约束、衍生时钟约束、输入延迟约束、输出延迟约束、时序例外约束、异步时钟约束等的顺序引导设计者创建约束。



