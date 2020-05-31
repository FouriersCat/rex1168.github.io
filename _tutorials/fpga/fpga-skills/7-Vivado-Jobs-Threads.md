---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Vivado中jobs和threads的区别？选择多个jobs能加快实现速度么？
description: "."
chapter: 1
category: fpga-skills
post-headings:
author-link: #
no-video: true
publish-date: 2020-05-30
thumbnail: /static/img/course_cover-small/fpga-skills.png
index: 7
---


 



&emsp;&emsp;在用Vivado对工程编译时，会弹出下面的对话框：

*备注：虽然FPGA不能叫编译，但很多工程师为了方便起见，将综合+实现+生成bit文件的过程统称为编译了，这种说法大家理解就好。*

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200503211013511.png)



很多工程师都会选择多个jobs进行编译，以为这样会更快一些，而且这个jobs的数量跟本地CPU的线程数是一致的，这就更加让工程师们认为这个选项就是多线程编译了。

&emsp;&emsp;但对Vivado更加熟悉的工程师，肯定会知道，Vivado中的多线程是通过tcl脚本去设置的，而且目前最大可使用的线程数是8个，那这个jobs跟多线程有什么关系呢？使用多个jobs能加快编译速度么？



&emsp;&emsp;我们首先来看jobs的定义，在UG904中这样写道：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200503211059859.png)

因此，这个jobs是我们在同时有多个runs在跑的时候才起效的，如果只有一个Design run，那这个参数是不起效的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200503211123307.png)

</br>
</br>

&emsp;&emsp;对于多线程，在UG904中是这样说的：

</br>

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020050321114548.png)

Implementation过程，最多使用8个线程，在Windows上，默认是2线程；在Linux上，默认是8线程；我们可以通过tcl脚本改变线程数，使用方式为：
```
set_param  general.maxThreads 8
```
也可以获取当前使用的线程数：
```
get_param  general.maxThreads
```

&emsp;&emsp;为了更直观的体现jobs和threads的使用方式，下面进行惨无人道的试验：

首先，处理器为Intel的i7-8700k,6核12线程，下面记录的时间仅是Implementation的时间，不包括Synthesis。

 - Test1为默认的2线程，最大jobs（12）的情况下，需要19min；
 - Test2把jobs降为1，线程还是2，仍然需要19min，说明在只有一个Design Run的时候，jobs的数量不影响编译时间；
 - Test3是采用8线程，jobs跟Test1相同，都是12，此时需要17min，比默认的2线程快了2min；
 - Test4是在8线程的基础上，把jobs降为1，此时还是17min，再次验证了单个design run时，jobs的数量不影响编译时间；
 - Test5是总共有6个Implementation的Design runs，采用8线程12jobs同时跑；
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200503211209412.png)

在下面这个图中也可以看出来，此时CPU的利用率已经很高了。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200503211224655.png)


 - Test6也是6个Implementation的Design runs，8线程，但jobs设置为1，此时可以看出，只有一个design run在跑，其他都在等待中，要等这个前一个跑完后，后面的才会开始。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200503211242306.png)



Num | jobs| threads | time
----|----|---------| ---
Test1|12  | 2       | 19min
Test2|1   | 2       | 19min
Test3|12  | 8       | 17min
Test4|1   | 8       | 17min
Test5|12  | 8       | 38min
Test6|1   | 8       | 120min


从上面的试验，我们可以总结如下：

1. 对于单个design run，jobs的数量不影响编译速度；
2. 在Windows上，默认的线程数是2，我们可以通过tcl来改变线程数，**但每打开工程后都要重新设置一下**，多线程会使综合实现的时间缩短，但效果并不是很明显；网上有个工程师说本来2线程1小时编译完的工程，用了8线程后，编译时间为50分钟；
3. 在多个design runs时，jobs的数量是当前可以同时运行的design run的个数。
































