---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Matlab中相见恨晚的命令（持续更新）
description: "."
chapter: 1
category: optimize
post-headings:
author-link: #
no-video: true
publish-date: 2020-05-30
thumbnail: /static/img/course_cover-small/matlab-skills.png
index: 3
---




&emsp;&emsp;知乎上有个“有哪些让人相见恨晚的Matlab命令”的话题，很多答主提供的命令确实很实用，为了更方便大家的学习，我就知乎上的答案和我自己想到的都综合整理成了一篇文章，把我觉得很实用的指令整理出来。

[知乎原答案链接](https://www.zhihu.com/question/24499729)


### **dbstop if error**
&emsp;&emsp;如果运行出错，matlab会自动停在出错的那一行，并保存相关变量，非常好用的指令，谁用谁知道。

配合这个指令一起使用的是`dbup`和`dbdown`，这两个指令用于workspace间切换查看变量调试，`dbup`是调到上层workspace，`dbdown`是返回。

### **linkaxes**
&emsp;&emsp;当使用`subplot`作图时，想要同时缩放多幅图，就可以使用该指令
```Matlab
figure;
ax(1) = subplot(211);
plot(sig1);ylim([-2,2]);grid on
ax(2) = subplot(212);
plot(sig2);ylim([-2,2]);grid on
linkaxes(ax, 'xy');                    %x轴和y轴都同步，也可以单独指定x或者y
```

效果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052719062913.gif)

### **yyaxis right**
&emsp;&emsp;当我们要把两个波形画到一个图中时，大家都知道可以使用`hold on`，但如果两个波形的值域相差很大时，如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052719120529.png)
&emsp;&emsp;如果我们使用了`yyaxis right`
```Matlab
plot(sig1);
yyaxis right
plot(sig2);
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527191402668.png)
### 折叠注释内容
&emsp;&emsp;使用下面的注释方式可以将注释后的内容折叠起来
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527191839461.png)

### deal
&emsp;&emsp;可简化初始化的代码
```Matlab
% 要初始化4个矩阵
A = zeros(3,4);
B = zeros(3,4);
C = zeros(3,4);
D = zeros(3,4);
```
使用了`deal`，一行搞定：
```Matlab
[A,B,C,D] = deal(zeros(3,4));
```

如果初始化内容不同，也可以使用`deal`,
```Matlab
%% 原始程序
A = zeros(3,4);
B = [];
C = ones(2);
D = cell(2);
%% 使用deal的程序
[A,B,C,D] = deal(zeros(3,4), [], ones(2), cell(2));
```

### 查看Matlab函数源代码

&emsp;&emsp;有时觉得自己写的程序不规范，想参考Matlab官方是怎么写的，比如想看SVM分类器是怎么写的，可以输入
```Matlab
edit ClassificationSVM.m
```

### fdatool
&emsp;&emsp;设计滤波器必备利器，在Matlab2019中还能用，但提示以后要换成`filterDesigner`了，设计完滤波器后可以把系数导出，还能直接导出成Xilinx FPGA开发软件可以识别的coe文件。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527200942781.png)

### 条件断点

比如我们写了下面的代码：

```Matlab
n = 1000;
for i=1:n
    xxx
end
```
我们想在i=100的时候停下来，可以在编辑器边缘右键，设置条件断点。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527201229279.png)
再设置我们想要的条件就好了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527201257913.png)

先整理到这里，后面不定期更新

