---
youtube_id: h3bgf9cDKQM
qq_video_id: q0830rmv5cl
b_av: 61523729
b_cid: 106765486
b_page: 30
title: 复数 分数
description: "Julia中的复数和分数"
chapter: 2
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 6
---

## 复数和分数

**复数**

```Julia
x = 1 + 2im
(1 + 2im)*(2 - 3im)
>>8 + 1im
(1 + 2im)^2
>>-3 + 4im
2(1 - 1im)
>>2 - 2im
```
运算优先级
```Julia
2/5im  #表示2/(5*im)
2/5im==-2/5*im
```
复数的其他运算
```Julia
sqrt(2 + 3im)
cos(1 + 2im)
exp(1 + 2im)
abs(1 + 2im)
sqrt(-1)  #error
sqrt(-1 + 0im)
x = 1
y = 2
z1 = x + y*im
z2 = complex(x,y)
real(z1)
angle(1+2im)
```
**分数**
```Julia
2//3
2//4
>>1//2
numerator(2//3) #分子
denominator(2//3) #分母
float(1//2)
isequal(float(a//b),a/b)
```


