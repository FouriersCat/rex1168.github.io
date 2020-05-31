---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: 什么是噪声温度？-174dBm/Hz又是什么？
description: "."
chapter: 1
category: signal-processing-theory
post-headings:
author-link: #
no-video: true
publish-date: 2020-05-30
thumbnail: /static/img/course_cover-small/signal-processing.png
index: 4
---


 


&emsp;&emsp;在信号处理系统中，我们经常会听到噪声温度这个概念，而且噪声温度的量纲也是K(凯氏温度)， 那噪声温度是什么意思呢？为什么会用温度来衡量噪声的大小？

K和℃的换算关系：
```
[℃] =  [K] - 273.15
```

我们常说的绝对零度就是−273.15℃，即0K。



&emsp;&emsp;首先，要明确的一点是：噪声温度其实就是噪声功率的一种表示方法，至于为什么要用温度来表示功率大小？直接用功率不好么？

&emsp;&emsp;要解释噪声温度，就要从噪声说起。

&emsp;&emsp;根据噪声产生的机理，大致可以分为五大类：热噪声(Thermal Noise)，散粒噪声(Shot Noise)，闪烁噪声(Flicker Noise)，等离子体噪声(Plasma Noise)，量子噪声(Quantum Noise)。

&emsp;&emsp;热噪声是最基本的一种噪声，可以说是无处不在的。热噪声又称为Johanson或Nyquist噪声，是由电子的热运动产生的。在绝对零度(0 K)以上，就会存在自由电子的热运动。因此，现实中的所有器件，都会产生热噪声。热噪声的功率谱密度不随频率变化，称为白噪声，又因服从Gauss概率密度分布，所以又称为高斯白噪声。


&emsp;&emsp;将一个电阻置于温度为T (开尔文温度)的环境中，电阻中的自由电子随机运动，动能与温度T成正比。电子的随机运动会产生小的随机电压波动，此时电阻相当于一个噪声源。随机电压的有效值为

$$
V_{n}=\sqrt{\frac{4 h f B R}{e^{h f / k T}-1}}
$$
其中，
```
h--普朗克常数，用以描述量子大小， h=6.62607015×10-34 J·s
k---玻尔兹曼常数，是有关于温度及能量的一个物理常数，k=1.38064852x10^-23 J/K
T--开尔文温度
B--系统带宽
f--系统中心频率
R--电阻阻值
```

该公式在RF频段，可以根据Rayleigh-Jeans（瑞利-琼斯）进行近似，由于hf << kT，因此：
$$
e^{h f / k T}-1 \cong \frac{h f}{k T} \\
V_{n}=\sqrt{4 k T B R}
$$

可以看出，噪声电压有效值与频率无关，当系统带宽确定后，噪声电压有效值是个常数，功率也是一个跟频率无关的常数，因此它是一种白噪声。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200503220609402.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5naGFpanVuMjAxMw==,size_16,color_FFFFFF,t_70#pic_center)



&emsp;&emsp;将电阻等效为一个噪声源，在其共轭匹配电路中，输出的噪声功率最大，为：

$$
P_{n, \max }=\left(\frac{V_{n}}{2 R}\right)^{2} R = {\frac{V_{n}^{2}}{4 R}}=k T B
$$

从公式可以看出，电阻输出的最大噪声功率只与当前温度及系统带宽有关，且呈线性关系，与电阻值无关。


&emsp;&emsp;电阻是一种无源器件，因此可以只考虑它的热噪声，但有源器件的噪声就相对复杂，为了分析的方便，我们把所有噪声都等效为热噪声:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200503220651730.png#pic_center)


共轭匹配时，器件输出的最大噪声功率为:

$$
N_{o}=k T_{e} B
$$

其中，Te为器件的等效噪声温度，B为器件的有效带宽。

&emsp;&emsp;通过这种表示方式，有源器件的白噪声就可以用噪声温度为某个值的电阻的噪声来描述了，更方便整个系统的分析，而且一般天线的噪声功率都比较低，用噪声温度来表示数值更加直观，比如卫星对地接收天线可能是几百K的量级，换算成功率的话数值就太小了，看起来不是很直接。

&emsp;&emsp;讲到这里，就得提一个关键的数字：-174dBm/Hz。什么意思呢？

在温度为290K，即16.85℃时，此时噪声的功率谱密度为：

$$
\frac{N_{0}}{B} = \frac{kT_{0}B}{B}=kT_{0}
$$

对该值取个db
$$
10*log10(kT_{0}) = 10*log10(1.38064852 * 10^-23 * 290) = -203.98 W/Hz = -173.98 dBm/Hz ≈ -174 dBm/Hz
$$

因此，-174dBm/Hz是噪声的功率谱密度，当带宽为1Hz时，噪声功率为-174dBm，但噪声功率并不是噪底，因此很多人说-174dBm就是噪底这种说法其实是有问题的。














