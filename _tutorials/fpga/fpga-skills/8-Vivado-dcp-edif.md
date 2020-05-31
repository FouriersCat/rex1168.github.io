---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Vivado中模块封装成edif和dcp
description: "."
chapter: 1
category: fpga-skills
post-headings:
author-link: #
no-video: true
publish-date: 2020-05-30
thumbnail: /static/img/course_cover-small/fpga-skills.png
index: 8
---


 



&emsp;&emsp;我们完成Vivado的工程后，大部分情况不能把整个工程的源代码都直接给客户或者其他工程师，需要我们先进行一些封装后再给他们，就像软件代码中会编译成dll后再Release给别人。


&emsp;&emsp;在Vivado中，常用的三种封装形式有三种：
 - IP
 - edif
 - dcp

这三种封装形式在使用上都是相似的，都是我们只提供模块的接口让用户去调用。

&emsp;&emsp;这篇文章我们讲一下封装成edif和dcp的步骤、区别、注意事项。

**封装成edif**

1. 将需要封装的模块设置为顶层模块；
2. 在综合选项中去掉IOBuffer，具体操作为在在综合设置窗口的Options下面最后一项`More Options`一栏写入`-no_iobuf`;


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200503135459946.png)



3. 综合完成后，Open Synthesized Design，并在TCL Console中输入：
```
write_edif path/xx.edif
```
4. 例化时，要保留一个跟edif同名的hdl文件，且文件中只保留module的接口。

**封装成dcp**

1. 将需要封装的模块设置为顶层模块；
2. 在综合选项中的Options下面最后一项`More Options`一栏写入`-mode out_of_context`;


![在这里插入图片描述](https://img-blog.csdnimg.cn/2020050313563751.png)



3. 综合完成后，Open Synthesized Design，并在TCL Console中输入：
```
write_checkpoint -noxdef path/test.dcp
```
4. 例化时，保留dcp文件即可，如果该dcp文件的接口中有parameter，例化时应该将parameter去掉。

dcp模块的接口如下：
```
module dcp_demo #(
 paramter paramt1 = 1,
 paramter paramt1 = 2
)(
 input in1,
 output o1
)
```
则例化方式如下，不能有包含parameter，否则会报错
```
dcp_demo (
 .in1 (in1),
 .o1  (o1)
 );
```


**封装成edif和dcp的区别**

1. 封装edif文件时，不能将XIlinx的IP Core封装在文件中，但dcp文件可以；
2. 例化时的区别，edif是网表文件，例化时需要增加同名的hdl文件，但dcp文件其实就是个压缩文件，例化时只使用dcp文件即可，不需要再加入同名的hdl文件。



**封装dcp文件时的注意事项**

1. 在将文件设为顶层文件时，接口中的parameter一定记得要修改成实际值。这是因为我们都习惯于在hdl中例化模块时指定parameter的参数，这往往跟模块中的参数值是不一样的；
2. 输入的接口例化时不能悬空，有的接口赋0即可，但有的接口赋0后在Implementation时会报error；
3. 需要额外注意inout接口，很多工程师在使用inout接口时，习惯于自己用hdl来处理，比如I2C的inout接口我们经常会这么写：
```
assign scl_in = i2c_scl;
assign i2c_scl = i2c_scl_oe ? i2c_scl_o : 1'bz
```
但这么写的话，生成的dcp在Implementation时会报error，我们要使用Xilinx的IOBUF的原语来处理，改成下面的写法：
```
IOBUF #( .DRIVE(12), .IBUF_LOW_PWR("TRUE"), .SLEW("SLOW") ) SCL_inst (.O(scl_in),.IO(i2c_scl), .I(i2c_scl_o),.T(~(enable & ~scl_out)));
```

4. 在我们把文件设为顶层文件后，需要将工程中的约束文件先Disable掉，因此dcp文件中会带有当前工程的约束信息，如果没有Disable掉，那么在例化生成的dcp时会引入新的约束文件。













