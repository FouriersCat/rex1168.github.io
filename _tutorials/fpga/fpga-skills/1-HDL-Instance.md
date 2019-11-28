---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: vivado生成HDL例化模板
description: "."
chapter: 1
category: fpga-skills
post-headings:
author-link: #
no-video: true
publish-date: 2019-11-26
thumbnail: /static/img/course_cover-small/fpga-skills.png
index: 1
---


 
 

本篇文章的方法其实不算很好，实际使用起来，很不好用，每次都要先点击’Open Elaborated Design’后才能使用（很慢），而且生成的例化模板接口顺序也并不是按照原始HDL中的接口顺序，用了一次后基本就没再用过，所以就自己写了一个生成例化文件的Python 脚本。


链接如下：

[生成Verilog HDL例化模板](https://zhuanlan.zhihu.com/p/83601691)



在ISE的开发中，可以很方便的生成HDL文件的例化模板，但vivado中，很多同学并没有找到这个功能，其实功能还是有的，只不过在vivado中很多功能可以通过tcl脚本实现，因此Xilinx就把这些功能从图形化中去除了。下面我们看vivado中怎么生成HDL的例化模板。


1. 首先要安装`Design Utilities`, 点击`Tools->Xilinx Tck Store`选项，如下图1所示。

<p align="center">
  <img src="https://github.com/Bounce00/pic/blob/master/fpga/vivado1.png?raw=true">
</p>




1.点击`Install`, 安装`Design Utilities`,如下图2所示。



<p align="center">
  <img src="https://github.com/Bounce00/pic/blob/master/fpga/vivado2.png?raw=true">
</p>



之后再生成例化模板时就不需要再安装了，直接进行下面的步骤就行。

2.把要生成例化模板的HDL文件设置为top文件，并点击`Open Elaborated Design`，如下图3所示。


<p align="center">
  <img src="https://github.com/Bounce00/pic/blob/master/fpga/vivado3.png?raw=true">
</p>



3.在`Tcl Console`中执行`xilinx::designutils::write_template -template -verilog`指令，就可以看到下图4所示，根据提示内容，去对应位置找到生成的例化模板文件即可。



<p align="center">
  <img src="https://github.com/Bounce00/pic/blob/master/fpga/vivado5.png?raw=true">
</p>



如果没有执行第3步操作，就会出现下面的情况


<p align="center">
  <img src="https://github.com/Bounce00/pic/blob/master/fpga/vivado4.png?raw=true">
</p>



可以看出，提示了Warning，并且生成了一个没有名字的.v文件。如果打开文件的话，会发现文件中并没有有用内容。

好，到这里，就已经正确生成了HDL例化模板了。但每次都需要输入tcl指令还是略显麻烦，我们可以设置一个tcl的按钮，一键生成。操作如下：


4.点击`Tools->Custom Commands->Customize Commands`，如下图6所示。




<p align="center">
  <img src="https://github.com/Bounce00/pic/blob/master/fpga/vivado6.png?raw=true">
</p>



进去之后，按照下图的操作步骤，生成tcl指令。



<p align="center">
  <img src="https://github.com/Bounce00/pic/blob/master/fpga/vivado7.png?raw=true">
</p>



操作完后，就可以在vivado的菜单栏中看到tcl的按钮标志，如下图8所示。


<p align="center">
  <img src="https://github.com/Bounce00/pic/blob/master/fpga/vivado8.png?raw=true">
</p>



以后在敲指令的步骤就可以替代为直接点击该按钮即可。

OK，以上就是全部操作步骤了。但这种方式生成的例化模板，是按照接口类型排序的，前面是`input`,中间是`output`，最后是`inout`，并不是按照我们原始的端口排序，这可能会造成一点不便。






