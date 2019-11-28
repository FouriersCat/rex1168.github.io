---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: 信号的频谱 频谱密度 功率谱密度 能量谱密度的区别
description: "."
chapter: 1
category: signal-processing-theory
post-headings:
author-link: #
no-video: true
publish-date: 2019-11-26
thumbnail: /static/img/course_cover-small/signal-processing.png
index: 1
---


 
 
&emsp;&emsp;这几个概念，对于刚学信号系统的同学甚至对于很多信号处理的老手来说，都是分不清楚的，下面我们就一一解释这几个概念。

&emsp;&emsp;要解释几个概念，就要首先说一下信号的能量和功率。信号按能量是否有限，可以分为：能量信号和功率信号，能量信号的能量是有限的，功率信号的能量是无限的。下面我们具体解释一下这两个概念。

&emsp;&emsp;在信号系统领域，通常把信号功率定义为电流在单位电阻（1欧姆）上消耗的功率，即归一化功率P。


<p align="center">
<img src="https://latex.codecogs.com/gif.latex?P=\frac{U^2}{R}=I^2R=U^2=I^2" title="P=\frac{U^2}{R}=I^2R=U^2=I^2" />
</p>


一般的，用s表示电流或电压，信号能量是信号瞬时功率的积分，因此，信号的能量为：

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?E=\int_{-\infty}^{&plus;\infty}&space;s^{2}(t)&space;d&space;t" title="E=\int_{-\infty}^{+\infty} s^{2}(t) d t" />
</p>

若信号能量是一个正的有限值，即 <img src="https://latex.codecogs.com/gif.latex?0<E<\infty" title="0<E<\infty" />，则称为能量信号，此时的平均功率定义为：

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?P=\lim&space;_{T&space;\rightarrow&space;\infty}&space;\frac{1}{T}&space;\int_{-T&space;/&space;2}^{&plus;T&space;/&space;2}&space;s^{2}(t)&space;d&space;t" title="P=\lim _{T \rightarrow \infty} \frac{1}{T} \int_{-T / 2}^{+T / 2} s^{2}(t) d t" />
</p>

&emsp;&emsp;由于积分里面是个有限值，而T是无穷大，因此P=0，所以能量信号的平均功率是0.
也就是说，如果P不是0（功率信号），那么积分的结果肯定是无穷大，也就说能量是无穷大。
所以，这里再重复一遍上面的结果：

&emsp;&emsp;能量信号：能量有限，平均功率为0；

&emsp;&emsp;功率信号：能量无穷大，功率非0。

&emsp;&emsp;举两个简单例子，单位冲激信号就是一个典型的能量信号，因为它在无穷大区间上的积分是1，是个有限值。而阶跃信号（或者某个电压非0的直流信号或周期信号）就是功率信号，因为它在无穷大区间上的积分是无穷大。

&emsp;&emsp;搞清楚上面两个概念之后，我们再来看信号的频率特性分类，有四种：功率信号的频谱、能量信号的频谱密度、功率信号的功率谱（密度）和能量信号的能量谱密度

**功率信号的频谱：**

&emsp;&emsp;周期性功率信号的频谱函数为：

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?c_{n}=c\left(n&space;f_{0}\right)=\frac{1}{T_{0}}&space;\int_{-T&space;/&space;2}^{&plus;T&space;/&space;2}&space;s(t)&space;\mathrm{e}^{-j&space;2&space;\pi&space;f_{0}&space;t}&space;d&space;t" title="c_{n}=c\left(n f_{0}\right)=\frac{1}{T_{0}} \int_{-T / 2}^{+T / 2} s(t) \mathrm{e}^{-j 2 \pi f_{0} t} d t" />
</p>

式中，

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?f_{0}=1/T_{0}" title="f_{0}=1/T_{0}" />
</p>

n为整数.

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?c_{0}=\frac{1}{T_{0}}&space;\int_{-T&space;/&space;2}^{&plus;T&space;/&space;2}&space;S(t)&space;d&space;t" title="c_{0}=\frac{1}{T_{0}} \int_{-T / 2}^{+T / 2} S(t) d t" />
</p>

&emsp;&emsp;一般来说， c(n) 是一个复数，代表在频率 <img src="https://latex.codecogs.com/gif.latex?nf_{0}" title="nf_{0}" /> 上信号分量的复振幅。

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?c_{n}=\left|c_{n}\right|&space;e^{j&space;\theta_{n}}" title="c_{n}=\left|c_{n}\right| e^{j \theta_{n}}" />
</p>

&emsp;&emsp;对于周期性功率信号来说，其频谱函数cn（cn就是s(t)的傅里叶系数）是离散的，只有在f0的整数倍上取值。由于n可以取负值，所以在负频率上 c_{n} 也有值，通常称为双边频谱，双边普中负频谱仅在数学上有意义；在物理上，并不存在负频率。但我们可以找到物理上实信号的频谱和数学上的频谱函数的关系，对于物理可实现信号有

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?c(-n)=conj(c(n))" title="c(-n)=conj(c(n))" />
</p>

即频谱函数的正频率部分和负频率部分间存在复数共轭关系。这就是说，负频谱和正频谱的模是偶对称的，相位是奇对称的。

&emsp;&emsp;对于非周期性的功率信号，原则上可以看成周期等于无穷大，仍然可以按照以上公式，但是实际上的积分是难以计算的。

**能量信号的频谱密度：**

&emsp;&emsp;设一个能量信号为 s(t) ，则将它的傅里叶变换定义为它的频谱密度:

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?S(\mathrm{f})=\int_{-\infty}^{&plus;\infty}&space;s(t)&space;\mathrm{e}^{-j&space;2\pi&space;f&space;t}&space;d&space;t" title="S(\mathrm{f})=\int_{-\infty}^{+\infty} s(t) \mathrm{e}^{-j 2\pi f t} d t" />
</p>

&emsp;&emsp;傅里叶变换存在的条件是f(t)在负无穷到正无穷的区间内积分为有限大，即绝对可积。因此傅里叶变换的结果就是能量信号的**频谱密度**，但为了统一说法，我们一般也叫频谱。
**(我们平时所说的做个fft看频谱，其实是指的频谱密度）**

&emsp;&emsp;那为什么叫频谱密度呢？因为能量信号能量有限，并分布在连续的频谱轴上，所以在每个频点f上信号的幅度是无穷小，只有在一小段频率间隔df上才有确定的非零振幅。所以，能量信号的频谱都是0，频谱密度才有意义。

&emsp;&emsp;能量信号的频谱密度s(f)和周期性功率信号的频谱Cn的区别主要为：

&emsp;&emsp;1. .S(f)是连续谱，Cn是离散谱。即周期对应离散，非周期对应连续。

&emsp;&emsp;2. S(f)的单位是V/Hz，Cn的单位是V。
从傅里叶变换的公式可以看出，s(t)在时间维上的积分，结果的量纲应该是V*s = V/Hz，所以傅里叶变换的结果是频谱密度。

&emsp;&emsp;这里多说一点，量纲是个好东西，很多公式不理解的时候，把量纲分析一下，能起到很大作用。

&emsp;&emsp; 看到这里，可能有点明白了。但再回想一下信号系统中最常见的正弦信号，这是个功率信号，但我们平时好像一直在说它的傅里叶变换，也并没有什么太大问题。这是因为引入了单位冲击函数 <a href="https://www.codecogs.com/eqnedit.php?latex=\delta(t)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\delta(t)" title="\delta(t)" /></a> ，其性质如下

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?\left\{\begin{array}{c}{\int_{-\infty}^{&plus;\infty}&space;\delta(t)&space;d&space;t=1}&space;\\&space;{\delta(t)=0&space;\quad(t&space;\neq&space;0)}\end{array}\right." title="\left\{\begin{array}{c}{\int_{-\infty}^{+\infty} \delta(t) d t=1} \\ {\delta(t)=0 \quad(t \neq 0)}\end{array}\right." />
</p>

<img src="https://latex.codecogs.com/gif.latex?\delta(t)" title="\delta(t)" /> 在物理上是不可实现的，但在数学上，<img src="https://latex.codecogs.com/gif.latex?\delta(t)" title="\delta(t)" /> 可以用某些函数的极限来描述。例如用抽样函数的极限描述：

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?\int_{-\infty}^{&plus;\infty}&space;\frac{k}{\pi}&space;S&space;a(k&space;t)&space;d&space;t=1&space;\rightarrow&space;\delta(t)=\lim&space;_{k&space;\rightarrow&space;\infty}&space;\frac{k}{\pi}&space;S&space;a(k&space;t)" title="\int_{-\infty}^{+\infty} \frac{k}{\pi} S a(k t) d t=1 \rightarrow \delta(t)=\lim _{k \rightarrow \infty} \frac{k}{\pi} S a(k t)" />
</p>

换句话说，抽样函数的极限就是冲激函数。

&emsp;&emsp;有了冲激函数，我们就可以把功率信号当做能量信号看待，计算其频谱密度，功率信号在某些频率上的功率密度为无穷大。但是我们可以用冲击函数来表示这些频率分量。比如：

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?s(t)=\cos&space;\left(2&space;\pi&space;f_{0}&space;t\right)&space;\rightarrow&space;S(\mathrm{f})=\frac{1}{2}\left[\delta\left(f-f_{0}\right)&plus;\delta\left(f&plus;f_{0}\right)\right]" title="s(t)=\cos \left(2 \pi f_{0} t\right) \rightarrow S(\mathrm{f})=\frac{1}{2}\left[\delta\left(f-f_{0}\right)+\delta\left(f+f_{0}\right)\right]" />
</p>

 &emsp;&emsp;因此，只要引入冲激函数，我们同样可以求出一个功率信号的频谱密度，换句话说，引用了冲激函数就能把频谱密度推广到功率信号上，即我们可以直接对功率信号做傅里叶变换。这样把傅里叶变换的结果统称为频谱（严格来说应该是频谱密度）。

**能量信号的能量谱密度：**

&emsp;&emsp;根据Parseval定理，信号时域能量和频域能量相等，有

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?E=\int_{-\infty}^{&plus;\infty}&space;s^{2}(t)&space;d&space;t=\int_{-\infty}^{&plus;\infty}&space;S^{2}(f)&space;d&space;f" title="E=\int_{-\infty}^{+\infty} s^{2}(t) d t=\int_{-\infty}^{+\infty} S^{2}(f) d f" />
</p>


&emsp;&emsp;我们将 <img src="https://latex.codecogs.com/gif.latex?|S(f)|^{2}" title="|S(f)|^{2}" /> 称为能量信号的能量谱密度，它表示在频率f处宽度为df的频带内的信号能量，或者可以看做是单位频带内的信号能量。

**功率信号的功率谱（密度）：**

&emsp;&emsp;这里为什么要把密度加括号呢？因为当我们说功率谱的时候，其实指的就是功率谱密度，它表示单位频率的信号功率。

&emsp;&emsp;可能网上有人提过这种说法：若信号能量为E，时间为T，频带为F，则功率谱是表示为E/T；而功率谱密度是表示为E/T/F。

&emsp;&emsp;这种说法其实是有问题的，因为E/T表示的是平均功率，而不是功率谱，平均功率并没有谱的概念。

&emsp;&emsp;信号的平均功率定义为：

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?P=\lim&space;_{T&space;\rightarrow&space;\infty}&space;\frac{1}{T}&space;\int_{\frac{-T}{2}}^{\frac{T}{2}}|s(t)|^{2}&space;d&space;t=&space;\int_{-\infty}^{\infty}\lim&space;_{T&space;\rightarrow&space;\infty}&space;\frac{|S(f)|^{2}}{T}&space;d&space;f" title="P=\lim _{T \rightarrow \infty} \frac{1}{T} \int_{\frac{-T}{2}}^{\frac{T}{2}}|s(t)|^{2} d t= \int_{-\infty}^{\infty}\lim _{T \rightarrow \infty} \frac{|S(f)|^{2}}{T} d f" />
</p>


设 <a href="https://www.codecogs.com/eqnedit.php?latex=\vartheta(f)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\vartheta(f)" title="\vartheta(f)" /></a> 表示信号的功率谱密度，则有

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?P=\int_{-\infty}^{\infty}&space;\vartheta(f)&space;d&space;f" title="P=\int_{-\infty}^{\infty} \vartheta(f) d f" />
</p>

因此，信号的功率谱密度为：

<p align="center">
<img src="https://latex.codecogs.com/gif.latex?\vartheta(f)=\lim_{T&space;\to&space;\infty}\frac{|S(f)|^{2}}{T}" title="\vartheta(f)=\lim_{T \to \infty}\frac{|S(f)|^{2}}{T}" />
</p>













