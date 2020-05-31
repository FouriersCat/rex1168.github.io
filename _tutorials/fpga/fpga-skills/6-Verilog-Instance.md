---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: 生成Verilog例化模板
description: "."
chapter: 1
category: fpga-skills
post-headings:
author-link: #
no-video: true
publish-date: 2020-05-30
thumbnail: /static/img/course_cover-small/fpga-skills.png
index: 6
---


 


&emsp;&emsp;之前我写过一篇过于vivado生成HDL例化模板的文章。[链接](http://www.technomania.cn/tutorials/fpga/fpga-skills/1-HDL-Instance/)
&emsp;&emsp;但实际使用起来，发现很不好用，每次都要先点击'Open Elaborated Design'后才能使用（很慢），而且生成的例化模板接口顺序也并不是按照原始HDL中的接口顺序，用了一次后基本就没再用过，所以就自己写了一个生成例化文件的Python 脚本。

具体见[github](https://github.com/Rex1168/FpgaTool)

注：需要安裝PyQt5

使用方法：   
 - 方法一：使用GUI
  1. 运行FpgaTool.py，通过命令行或者PyCharm均可
  2. 打开一个Verilog文件
  3. 点击“View HDL Inst”

具体操作步骤可[参考](https://raw.githubusercontent.com/Rex1168/FpgaTool/master/gif/FpgaTool.gif)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200530223326339.gif)


 - 方法二：直接使用脚本
   1. 运行genHdlInst.py，通过命令行运行“python genHdlInst.py top.v”
   2. 在当前目录下会产生一个inst_top.v的文件，内容即为top.v的例化模板
   
具体操作步骤可[参考](https://raw.githubusercontent.com/Rex1168/FpgaTool/master/gif/genHdlInst.gif)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200530225211901.gif)

如果觉得好用，记得在github上star一下，在此谢过


















