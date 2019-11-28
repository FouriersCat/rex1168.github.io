---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: 如何使用Git进行Vivado工程的管理
description: "."
chapter: 1
category: fpga-skills
post-headings:
author-link: #
no-video: true
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/fpga-skills.png
index: 4
---


 
 


&emsp;&emsp;git有多好用我就不用多说了，可谓是程序员必备技能之一。对于一般的软件代码来说，只需把源文件进行git管理即可。但对于FPGA工程师来说，使用git多多少少有些蛋疼，主要有下面几个问题：
 1. 有bd文件的工程中，只把bd文件加入git是不行的；
 2. 很多公司都会有一些积累下来的hdl文件，放到某个文件夹中，所有的工程目录下都会有这个文件夹，因此这个文件中会有很多不是当前工程的文件，因此git的文件中，必须包含当前工程的files list，否则就得重新添加文件；
 3. 如果把整个Vivado工程都放入git，非必要文件太多，既浪费git服务器的空间，Push和Pull的速度也会很慢；
 4. 很多FPGA工程师有自己的一套git管理Vivado的流程，但每次push前和Clone后都需要做很多操作。
 

&emsp;&emsp;最近我也一直在研究Vivado工程git的管理方式，查了网上很多方法，但有些操作略复杂，有些方法完全不起效，但好在最终综合整理出了一种相对简单的方式进行，也许这种方式不是最合适的，如果你有更好的方法，希望能分享出来。

**我们这篇文章只讲Vivado的工程，不包括HLS或者Sdk工程，因为这这两个工具都是纯C/C++/TCL的，git管理起来比较简单**

### 1. 工程目录

&emsp;&emsp;我们采用如下的目录结构，这种方式也是Xilinx官网某个教程上推荐的，
 - Work目录：Vivado的工程全都放在Work目录下，包括bd以及它的相关文件；
 - Src目录：放hdl的源文件以及IP Core的源文件；
 - Doc目录：放跟工程相关的文档，如果没有的话可以不加；
 - Mcs目录：放工程生成的bit和mcs文件，这个文件也看个人用途，如果觉得bit文件太大不想放在git上也无所谓，但个人觉得在调试的时候经常会用到之前测试好了某个bit；
 - Scripts目录：也是进行git管理的关键，里面放各种Tcl脚本；
 - 如果工程中有SDK/HLS/SsyGen，可以在该目录下再增加文件夹专门放置对应的工程文件。



<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/git1.png">
</p>


### 2. 操作步骤

1. 在打开的Vivado工程中，通过Tcl Console将目录切换到Work目录：

```
cd [get_property directory [current_project ]]
```

2. 生成新建工程的Tcl脚本，执行：

```
write_project_tcl {./Scripts/s1_recreate_project.tcl}
```

3. 依次生成bd文件的tcl脚本，比如工程中有两个bd文件`aa.bd`和`bb.bd`，打开`aa.bd`，并执行：

```
write_bd_tcl {./Scripts/s2_aa_bd.tcl}
```

打开`bb.bd`，并执行：

```
write_bd_tcl {./Scripts/s3_bb_bd.tcl}
```

这样，在Scripts文件夹中，就会多出三个文件：


<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/git2.png">
</p>


4. 在`.gitignore`文件中，把整个Work目录全部ignore掉，Src文件夹中根据实际情况进行ignore即可，HDL文件都保留，IP Core只留`.xci`文件；Scripts目录全部保留。

5. 到这一步，就可以进行Commit和Push了。

6. 把程序Clone下来后，首先要修改`s1_recreate_project.tcl`，需要修改的地方有两个：一个是新建工程时的路径，原始文件为：


<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/git3.png">
</p>

替换为：


<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/git4.png">
</p>


第二处需要更改的是删除files list中的bd文件信息，如下图中框起来的部分，这部分内容是将文件添加到工程中，但此时我们的bd文件还没有生成，因此这两个要删掉。bd文件是我们通过后面的tcl脚本添加进来的。



<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/git5.png">
</p>

7.  切换到Scripts目录


<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/git6.png">
</p>

8. 依次执行：

```
source ./s1_recreate_project.tcl
source ./s2_aa_bd.tcl
source ./s3_bb_bd.tcl
```

OK，到这里工程就Clone完成了！！！

下面说一下我操作的过程中碰到的几个问题：

1. 如果是DDR3的MIG IP Core，会有Configuration Files，如果我们修改了MIG，并重新生成后，就会经常出现这个Configuration Files找不到的情况，像下面这样：



<p align="center">
  <img src="https://raw.githubusercontent.com/Bounce00/pic/master/fpga/fpga_skills/git7.png">
</p>

这样就要我们在导出`s1_recreate_project.tcl`前要先把这个文件替换成正确的文件。如果MIG是放在到bd中，那么在上述的第6步修改`s1_recreate_project.tcl`中，也要把`mig_*.prj`文件删掉，因为这个文件是在新建MIG时自动生成的。

2. 有些朋友用bd的时候喜欢用wrapper.v的文件来封一层，这个wrapper.v是Vivado自动生成的，而且默认目录就是在Work的工程目录下，我们可以不用管这个wrapper.v的文件，等bd文件添加进去后，再手动生成一下即可；也可以不使用wrapper.v，直接例化bd文件。


3. 上面这种方法是在Vivado2017.2上进行的测试，如果是2017.4及以上的版本，`write_project.tcl`指令中需要增加`-use_bd_files`选项，也就是：
```
write_project_tcl -use_bd_files {./Scripts/s1_recreate_project.tcl}
```

如果不加这个选项，默认会把bd文件信息也导出,这样在运行时会提示么有发现bd文件的错误。

4. 如果是低版本的工程进行git后，要用高版本的Vivado打开，我们可以在导出bd时去掉IP的版本信息，也就是：
```
write_bd_tcl -no_ip_version {./Scripts/s2_aa_bd.tcl}
```

**总结**

&emsp;&emsp;我尝试了网上很多方法基本都不能直接使用，但他们既然把方法放到网上，说明是经过测试的，但可能测试的并不是特别全面，就是MIG的IP一样，如果我的工程中没有这个IP，那我也不知道在git时会出现这种问题。我也不能保证在使用别的IP时不会出现问题，但思路都是一样的，就是把工程的tcl脚本和bd的tcl脚本分开，先新建工程把非bd文件的内容加进来，再把bd的文件内容添加进来。













