---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Matlab中fft与fwelch有什么区别？如何用fft求功率谱？
description: "."
chapter: 1
category: signal-processing-theory
post-headings:
author-link: #
no-video: true
publish-date: 2020-05-30
thumbnail: /static/img/course_cover-small/signal-processing.png
index: 3
---


 
 
讲这个话题，就要先搞清楚频谱、功率谱的概念，可参考我的另一篇文章

[信号的频谱 频谱密度 功率谱密度 能量谱密度的区别](http://www.technomania.cn/tutorials/signal/signal-processing-theory/1-Basic-Concept/)

做信号处理的朋友应该都会fft比较熟悉，就是求傅里叶变换。我在这里也不再去讲这个函数了，但需要注意的一点：实信号的频谱关于0频对称，是偶函数，如果st = cos(2*pi*f0*t)+1;  t的长度为4000，那么0频的位置在第一个点，做fftshift后，0频的位置在低2001个点的位置，fft后的信号关于第2001个点对称，而不是4000个点左右对称。

pwelch是用来求功率谱的，采用Welch平均周期法对信号进行谱估计，它通过分段选取数据进行加窗求功率，再进行平均，pwelch函数的使用方式为：
```
pxx = pwelch(x,window,noverlap,nfft)
[pxx,f] = pwelch(x,window,noverlap,f,fs)
```

其中，
 - `X`表示输入序列；
- `window`：当window是一个数值时，表示窗函数长度，即分段长度`L`，默认的窗函数为hamming窗；当window是一个序列时，表示窗函数序列；
- `NFFT`表示FFT的点数，X为实数时，当`NFFT`是偶数时，`Pxx`的长度是`(NFFT/2+1)`；当`NFFT`是奇数时，`Pxx`的长度是`(NFFT+1)/2`；X为复数时，`Pxx`的长度就是`NFFT`，如果`NFFT`没有指定，则默认是256或者比`X长度大的2的N次幂
- `Fs` 绘制功率谱曲线的采样频率，默认值为1
`Pxx``表示功率谱估计值
- `F`表示Pxx值所对应的频率点 
- `NOVERLAP`指定分段重叠的样本数 ，如果`NOVERLAP=L/2`，则可得到重叠50%的Welch法平均周期图 

下面我们分别用fft和fwelch来求信号的功率谱。

```
clc;close all;clear all;

fs = 10e6;
N = 4000;
t = (0:N-1)/fs;
f0 = 1e5;

st = cos(2*pi*f0*t) + 1;
st_fft = fft(st);
psdx = abs(st_fft(1:end/2+1)).^2/fs/N;  %功率谱密度为能量谱密度除以时间，摸值的平方即为能量谱
psdx(2:end) = 2*psdx(2:end);            %乘2是因为fft结果是对称的，在计算功率时需要把功率加回来；第一个点是0频，这个点并不对称
freq = linspace(0,fs/2,length(psdx));
[pxx,f] = pwelch(st,rectwin(N),32,N,fs);
figure;plot(freq,psdx);title('fft方法求功率谱密度');grid on
figure;plot(f,pxx);title('fwelch方法求功率谱密度');grid on

```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200510232543364.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200510232700446.png)







