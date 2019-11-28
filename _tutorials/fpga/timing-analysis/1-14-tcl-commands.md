---
youtube_id: LvMuVkUgRnU
qq_video_id: 
b_av: 73006468
b_cid: 124857755
b_page: 14
title: Vivado时序约束中Tcl命令的对象及属性
description: ""
chapter: 4
category: timing-analysis
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 14
---





# Vivado时序约束中Tcl命令的对象及属性


&emsp;&emsp;在前面的章节中，我们用了很多Tcl的指令，但有些指令并没有把所有的参数多列出来解释，这一节，我们就把约束中的Tcl指令详细讲一下。

我们前面讲到过`get_pins`和`get_ports`的区别，而且我们也用过`get_cells`、`get_clocks`和`get_nets`这几个指令，下面就通过一张图直观展现它们的区别。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial55.png"  alt="" />
</center>

`get_clocks`后面的对象是我们之前通过`create_clocks`或者`create_generated_clocks`创建的时钟，不在硬件上直接映射。


&emsp;&emsp;我们再来看下各个命令的属性。
## 1. port

我们可以通过Tcl脚本查看port的所有属性，比如上面的wave_gen工程中，有一个port是`clk_pin_p`，采用如下脚本：
```
set inst [get_ports clk_pin_p]
report_property $inst
```
显示如下：

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial56.png"  alt="" />
</center>

`get_ports`的使用方法如下：
```
# 获取所有端口
get_ports *

# 获取名称中包含data的端口
get_ports *data*

# 获取所有输出端口
get_ports -filter {DIRECTION == OUT}

# 获取所有输入端口
all_inputs

# 获取输入端口中名字包含data的端口
get_ports -filter {DIRECTION == IN} *data*

# 获取总线端口
get_ports -filter {BUS_NAME != ""}
```

## 2. cell
 
按照上面的同样的方式，获取cell的property，如下：

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial57.png"  alt="" />
</center>

`get_cells`的使用方法如下：
```
# 获取顶层模块
get_cells *

# 获取名称中包含字符gen的模块
get_cells *gen*

# 获取clk_gen_i0下的所有模块
get_cells clk_gen_i0/*

# 获取触发器为FDRE类型且名称中包含字符samp
get_cells -hier -filter {REF_NAME == FDRE} *samp*

# 获取所有的时序单元逻辑
get_cells -hier -filter {IS_SEQUENTIAL == 1}

# 获取模块uart_rx_i0下两层的LUT3
get_cells -filter {REF_NAME == LUT3} *uart_tx_i0/*/*
```

## 3. pin
 
获取pin的property，如下：
<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial58.png"  alt="" />
</center>


`get_pins`的使用方法如下：
```
# 获取所有pins
get_pins *

# 获取名称中包含字符led的引脚
get_pins -hier -filter {NAME =~ *led*}

# 获取REF_PIN_NAME为led的引脚
get_pins -hier -filter {REF_PIN_NAME == led}

# 获取时钟引脚
get_pins -hier -filter {IS_CLOCK == 1}

# 获取名称中包含cmd_parse_i0的使能引脚
get_pins -filter {IS_ENABLE == 1} cmd_parse_i0/*/*

# 获取名称中包含字符cmd_parse_i0且为输入的引脚
get_pins -filter {DIRECTION == IN} cmd_parse_i0/*/*
```





## 4. net

获取net的property，如下：
<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial59.png"  alt="" />
</center>



`get_nets`的使用方法如下：
```
# 获取所有nets
get_nets *

# 获取名称中包含字符send_resp_val的网线
get_nets -hier *send_resp_val*
get_nets -filter {NAME =~ *send_resp_val*} -hier

# 获取穿过边界的同一网线的所有部分
get_nets {resp_gen_i0/data4[0]} -segments

# 获取模块cmd_parse_i0下的所有网线
get_nets -filter {PARENT_CELL == cmd_parse_i0} -hier

# 获取模块cmd_parse_i0下的名称中包含字符arg_cnt[]的网线
get_nets -filter {PARENT_CELL == cmd_parse_i0} -hier *arg_cnt[*]
```

&emsp;&emsp;这5个tcl指令的常用选项如下表：

命令      | -hierarchy  | -filter | -of_objects | -regexp | -nocase
----------|-------------|---------|-------------|---------|--
get_cells |      √      |     √   |     √       |   √     | √ 
get_nets  |      √      |     √   |     √       |   √     | √  
get_pins  |      √      |     √   |     √       |   √     | √ 
get_ports |             |     √   |     √       |   √     | √ 
get_clocks|             |     √   |     √       |   √     | √ 

&emsp;&emsp;这5个Tcl命令对应的5个对象之间也有着密切的关系，下图所示的箭头的方向表示已知箭头末端对象可获取箭头指向的对象。

<center>

<img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/timing_toturial60.png"  alt="" />
</center>

&emsp;&emsp;以wave_gen中的`clk_gen_i0`模块为例来说明上面的操作：


```
# 获取模块的输入引脚
get_pins -of [get_cells {clk_gen_i0/clk_core_i0}] -filter {DIRECTION == IN}

# 已知引脚名获取所在模块
get_cells -of [get_pins clk_gen_i0/clk_core_i0/clk_in1_n]

# 已知模块名获取与该模块相连的网线
get_nets -of [get_cells {clk_gen_i0/clk_core_i0}]

# 已知引脚名获取与该引脚相连的网线
get_nets -of [get_pins clk_gen_i0/clk_core_i0/clk_rx]

# 已知时钟引脚获取时钟引脚对应的时钟
get_clocks -of [get_pins clk_gen_i0/clk_core_i0/clk_rx]
```

需要注意的是：

 1. -hier不能和层次分隔符“/”同时使用，但“/”可出现在-filter中

 2. 可根据属性过滤查找目标对象

 3. -filter中的属性为：“==”（相等）、“!=”（不相等）、"=~"（匹配）、"!~"（不匹配）若有多个表达式，其返回值为bool类型时，支持逻辑操作













个人网站：http://www.technomania.cn/

微信公众号：Quant_Times    
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;Reading_Times
