---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: fvtool幅度归一化
description: "."
chapter: 1
category: optimize
post-headings:
author-link: #
no-video: true
publish-date: 2020-05-30
thumbnail: /static/img/course_cover-small/matlab-skills.png
index: 2
---





&emsp;&emsp;我们在用matlab设计滤波器后，可以用fvtool来看滤波器的频响，比如我们有了滤波器的系数后，直接用fvtool。

```matlab
fircoe = [0.018641, 0.018275,-0.020377,-0.071243,-0.049673,...
    0.094562, 0.28841, 0.37949, 0.28841, 0.094562,-0.049673,...
    -0.071243,-0.020377, 0.018275, 0.018641];
fvtool(fircoe);
```

<center>
   
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531174150748.png) 

</center>




&emsp;&emsp;从图中可以很容易看出，阻带衰减大概在40db左右。

&emsp;&emsp;但还有些滤波器，设计出来后增益并不是0，比如下面的cic滤波器。我们首先用`dsp.CICDecimator`设计了一个CIC滤波器，然后紧跟着一个CIC补偿滤波器，该补偿滤波是一个FIR滤波器。最后把这两个滤波器和CIC+FIR级联后的频响画到一图中。

```
fs = 100e6;
cic_rate = 4;
cic_flt = dsp.CICDecimator(cic_rate,1,5);
fpass=fs/cic_rate*0.35/2;
fstop=fs/cic_rate*0.5/2;
cic_comp = design(fdesign.ciccomp(cic_flt.DifferentialDelay, ...
        cic_flt.NumSections,fpass,fstop,.3,20,fs/cic_rate));
comp_fir = dsp.FIRFilter('Numerator',cmop_coe);
cascade_flt = cascade(cic_flt,comp_fir);
fvtool(cic_flt,comp_fir,...
cascade_flt,'Fs',[fs fs/cic2_rate fs])
yticks([-200:20:0])
```

<center>
    
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531174248835.png)

</center>



&emsp;&emsp;从图中可以看出，补偿的FIR滤波器增益大概为0，而CIC滤波器增益在60dB。怎么把CIC滤波器的频响显示的幅度也归一化到0呢？（因为归一化到0后，在文档中就很容易看出该滤波器阻带抑制特性）

&emsp;&emsp;可以在fvtool的窗口中，右键选择`Analysis Parameters...`，如下图。

<center>
    
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531174316511.png)

</center>



然后再勾选`Normalize Magnitude to 1(0dB)`，再点`Save as Default`。

<center>
    
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531174337751.png)

</center>




这样以后我们再用fvtool画滤波器频谱时，就会显示成归一化的样式。

<center>
    
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200531174406392.png)
    
</center>





