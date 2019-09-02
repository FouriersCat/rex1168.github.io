---
youtube_id: g1qRz2Q-rOc
qq_video_id: a0833nvochl
b_av: 61523729
b_cid: 106640381
b_page: 9
title: Julia科学计算之概率分布
description: "Julia中跟概率统计相关的库"
chapter: 12
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 32
---


## 概率分布
```
using Distributions
```

生成均值20，标准差2.0的正态分布模型
```
n1 = Normal(20,2.0)

params(n1)
>>(20,2.0)

fieldnames(typeof(n1))
>>(:μ, :σ)
```

Normal的类型是UnionAll
```
typeof(Normal)
>>UnionAll
```

生成满足n1模型的随机数
```
rand(n1,100)
```

二项分布
```
b = Binomial(20, 0.8)
rand(b, 1000)
```

概率密度
```
n = Normal()
pdf(n, 0.4)
>>0.36827014030332333

pdf(n, 0)      #标准正态分布在x=0处的概率密度为0.3989...
>>0.3989422804014327
```

分布函数
```
cdf(n, 0.4)
>>0.6554217416103242
```

分位数
```
quantile.(n, [0.25, 0.5, 0.95])
>>3-element Array{Float64,1}:
 -0.6744897501960818
  0.0               
  1.6448536269514717
```

对于随机数rand(10),按Normal分布分析它的参数值
```
fit(Normal, rand(10))
>>Normal{Float64}(μ=0.41375573240875224, σ=0.25565343114046984)
```



StatsBase库:里面也包含了统计学的常用函数

```
using StatsBase
```

```
a = collect(1:6)
b = collect(4:9)
countne(a,b) #按顺序对比  1!=4  2!=5 ... 6!=9
>>6
```

```
a = [1,2,3,4,5]
b = [4,1,3,2,5]
counteq(a,b) # 按顺序对比 两个向量中相等元素的个数
>>2
```


```
L1dist(a,b) # abs(a[1]-b[1]) + ... + abs(a[n]-b[n])
>>6.0

L2dist(a, b)  # sqrt((a[1] - b[1])^2 + ... + (a[n] - b[n])^2)
```

```   
meanad(a, b) # mean absolute deviation L1dist(a,b)/n
>>1.2
```

```
sample(a)  # 从a中采样一次数据
```

```
sample(a, 3) # 从a中采样3次数据，返回1维Array
>>3-element Array{Int64,1}:
 3
 2
 3
```


```
a1 = [1, 10, 20, 30]
a2 = [100, 200, 300]
sample!(a1, a2) # 从a1中，按照a2的类型取出length(a2)次数据
>>3-element Array{Int64,1}:
 20
 10
 30
```

```
a1 = [1, 10, 20, 30]
a2 = [100.0, 200.0, 300.0]
sample!(a1, a2)
>>3-element Array{Float64,1}:
  1.0
 30.0
  1.0
```

其他常用函数
```
x = [1.0, 2.0, 3.0, 4.0]
autocov(x)
autocov(x, [2,3])
autocor(x)
autocor(x, [2,3])
y = [3. , 4., 5., 6.]
crosscov(x, y)
crosscov(x, y, [2,3])
```


