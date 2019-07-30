---
youtube_id: EnueGYn3gNE
qq_video_id: f0831jwgmwh
b_av: 47775635
b_cid: 83682577
b_page: 10
title: Julia矩阵
description: "Julia中矩阵的基本操作"
chapter: 3
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 10
---


# 矩阵操作
既然是做科学计算，那肯定是少不了矩阵,先从简单的向量说起
首先定义一个简单的矩阵，在REPL中看返回的类型
```Julia
a = [1,2,3,4]
>>4-element Array{Int64,1}:
 1
 2
 3
 4
```
Julia中也可以像MATLAB中定义步进向量
```Julia
aa = (1:2:5)
aa.start
aa.step
aa.stop
first(aa)
step(aa)
last(aa)
```
```Julia
Int8[3, 4, 5]
>>3-element Array{Int8,1}:
 3
 4
 5
["one", "two", "threee"]
>>3-element Array{String,1}:
 "one"   
 "two"   
 "threee"
[true, "two", 1, 2.0]
>>4-element Array{Any,1}:
 true     
     "two"
    1     
    2.0   
[]
>>0-element Array{Any,1}
Int8[]
>>0-element Array{Int8,1}
a = (1, 2, 3)  # tuple
b = collect(a)
>>3-element Array{Int64,1}:
 1
 2
 3
c = collect(1:4) #不能直接写成[1:4]
c[2:end]
c1 = c      # c1与c的内存地址相同
c2 = c[:]   # c2是c的一个拷贝
c1[1] = 10
c
>>4-element Array{Int64,1}:
 10
  2
  3
  4
c2[1] = 20
c
>>4-element Array{Int64,1}:
 10
  2
  3
  4

```

```Julia
b = [1;2;3;4]
>>4-element Array{Int64,1}:
 1
 2
 3
 4
c = [1 2 3 4]
>>1×4 Array{Int64,2}:
 1  2  3  4
```
再来看矩阵拼接中的`空格` `,` `;`的区别
```Julia
x = ones(2,3)
y = zeros(2,3)
z = [x y]
>>2×6 Array{Float64,2}:
  1.0  1.0  1.0  0.0  0.0  0.0
  1.0  1.0  1.0  0.0  0.0  0.0
相当于hcat(x,y)
ndims(z)
>>2

z = [x,y]
>>2-element Array{Array{Float64,2},1}:
  [1.0 1.0 1.0; 1.0 1.0 1.0]
  [0.0 0.0 0.0; 0.0 0.0 0.0]
ndims(z)
>>1

[x;y]
>>4×3 Array{Float64,2}:
  1.0  1.0  1.0
  1.0  1.0  1.0
  0.0  0.0  0.0
  0.0  0.0  0.0
相当于vcat(x,y)
ndims(z)
>>2
```
那怎么把`[x,y]`也变成一个没有嵌套的矩阵呢？
```Julia
hcat([x,y]...) #表示把矩阵内部的Array作拼接
```
```
# 矩阵索引，从1开始
x[1]
>>1
x[6]
>>1
size(x)
>>(2,3)
length(x)
>>6
sum(x)
>>6
```

**矩阵运算**
```Julia
a = collect(reshape(1:6,2,3))
b = ones(2,3)
a .+ b
a .- b
a * b # error
a .* b
a * b'
a / b
a ./ b
```


函数对矩阵操作时，也要加`.`
```Julia
A = [1,2,3]
sin.(A)
>>3-element Array{Float64,1}:
 0.8414709848078965
 0.9092974268256817
 0.1411200080598672
```

**添加/删除/移动**
```Julia
a = [1,2,3]
push!(a, 4)
pop!(a)
push!(a, 4,5,6)
push!(a, [7,8,9])
>>error
append!(a, [7,8,9])
prepend!(a, [10,11,12])

arr = reshape(1:6, 2, 3)
circshift(arr, (0,1))
circshift(arr, (1,-2))

```





**对于矩阵的基本操作中，很多matlab中的函数Julia中基本也有，用法也基本一致**

eg.
```Julia
rand(10)
rand(2,3)
rand(Int32,2,3)
reshape(1:6, (2,3))
```

注：如果有些常用的数学函数发现在Julia中不能使用，比如mean()函数，则可以使用Statistics package。常用的数值分析的函数都在里面。

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/%E6%95%B0%E5%AD%A6%E8%BF%90%E7%AE%97.png)







