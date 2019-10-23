---
youtube_id: YNWKSf7Fvck
qq_video_id: 
b_av: 73006468
b_cid: 124799588
b_page: 6
title: FPGA时序约束理论篇之xdc约束优先级
description: "xdc约束优先级"
chapter: 1
category: timing-analysis
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-timing.png
index: 6
---



## xdc约束优先级

&emsp;&emsp;在xdc文件中，按约束的先后顺序依次被执行，因此，针对同一个时钟的不同约束，只有最后一条约束生效。

&emsp;&emsp;虽然执行顺序是从前到后，但优先级却不同；就像四则运算一样，+-x÷都是按照从左到右的顺序执行，但x÷的优先级比+-要高。

时序例外的优先级从高到低为：
 1. Clock Groups (set_clock_groups)
 2. False Path (set_false_path)
 3. Maximum Delay Path (set_max_delay) and Minimum Delay Path (set_min_delay)
 4. Multicycle Paths (set_multicycle_path)


`set_bus_skew`约束并不影响上述优先级且不与上述约束冲突。原因在于set_bus_skew并不是某条路径上的约束，而是路径与路径之间的约束。

&emsp;&emsp;对于同样的约束，定义的越精细，优先级越高。各对象的约束优先级从高到低为：
 1. ports->pins->cells
 2. clocks。

&emsp;&emsp;路径声明的优先级从高到低为：
 1. -from -through -to
 2. -from -to
 3. -from -through
 4. -from
 5. -through -to
 6. -to
 7. -through

***优先考虑对象，再考虑路径。***

&emsp;&emsp;Example1：
```
set_max_delay 12 -from [get_clocks clk1] -to [get_clocks clk2]
set_max_delay 15 -from [get_clocks clk1]
```
该约束中，第一条约束会覆盖第二条约束。

&emsp;&emsp;Example2：
```
set_max_delay 12 -from [get_cells inst0] -to [get_cells inst1]
set_max_delay 15 -from [get_clocks clk1] -through [get_pins hier0/p0] -to
[get_cells inst1]
```
该约束中，第一条约束会覆盖第二条约束。


&emsp;&emsp;Example3：
```
set_max_delay 4 -through [get_pins inst0/I0]
set_max_delay 5 -through [get_pins inst0/I0] -through [get_pins inst1/I3]
```
这个约束中，两条都会存在，这也使得时序收敛的难度更大，因为这两条语句合并成了：
```
set_max_delay 4 -through [get_pins inst0/I0] -through [get_pins inst1/I3]
```
