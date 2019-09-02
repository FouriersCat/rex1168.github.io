---
youtube_id: Ezs-KjHO0AQ
qq_video_id: d0833bofyvq
b_av: 61523729
b_cid: 106745882
b_page: 24
title: 其它优化技巧
description: "其它优化技巧"
chapter: 13
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 47
---


## 输出预分配

怎么理解呢？就是当我们在操作Array或者其他复杂类型时，我们预先为结果分配一个存储它的Array或其他类型，再进行计算，性能会显著提升

```
function xinc(x)
           return [x, x+1, x+2]
end

function loopinc()
   y = 0
   for i = 1:10^7
       ret = xinc(i)
       y += ret[2]
   end
   return y
end

function xinc!(ret::AbstractVector{T}, x::T) where T
   ret[1] = x
   ret[2] = x+1
   ret[3] = x+2
   nothing
end

function loopinc_prealloc()
   ret = Vector{Int}(undef, 3)
   y = 0
   for i = 1:10^7
       xinc!(ret, i)
       y += ret[2]
   end
   return y
end
```

```
@time loopinc()
>>0.428358 seconds (40.00 M allocations: 1.490 GiB, 10.25% gc time)

@time loopinc_prealloc()
>>0.024745 seconds (6 allocations: 288 bytes)
```

## copy和view

copy就是被复制的变量是原始变量的一份拷贝，存在内存拷贝的操作，而view只是映射关系，不存在内存拷贝

先举个view的例子

```
A = zeros(3, 3);
@views for row in 1:3
   b = A[row, :]
   b[:] .= row  # A的值也会相应改变
end
```

分别定义copy和view的函数
```
fcopy(x) = sum(x[2:end-1]);
@views fview(x) = sum(x[2:end-1]);
x = rand(10^6);
```

```
@time fcopy(x)
>>0.031803 seconds (7 allocations: 7.630 MiB, 85.25% gc time)

@time fview(x)
>>0.001218 seconds (6 allocations: 224 bytes)
```

## IO操作优化

在写文件时，对于某个变量，我们可以通过$的方式获取它的值，也可以直接使用变量本身

```
println(file, "$a $b")
```
下面的写法更好一些，因为上面这种方式先把表达式转成字符串，再写入文件中。而下面这种方式直接把值写入到文件中

```
println(file, a, " ", b)
```

## 其他性能优化小技巧

 - 避免不必要的Array，比如计算x,y,z的和时，使用x+y+z，不要用sum([x,y,z])

 - 计算复数模值平方时，使用`abs2(z)`，不要使用`abs(z)^2`，简而言之，有现成的函数就不要自己写计算过程

 - 用div(x,y)代替trunc(x/y)，用fld(x,y)代替floor(x/y)，用cld(x,y)代替ceil(x/y)，原因也是有现成的函数就不要自己写计算过程

```
function test_loop(r)
    for i in 1:10000
        for j in length(r)
            r[j] = tanh(r[j])
        end
    end
end

function test_vectorize(r)
    for i in 1:10000
        @. r = tanh(r)
    end
end
```

```
r = rand(1000)

@time test_vectorize(r)
>> 0.126987 seconds (4 allocations: 160 bytes)

@time test_loop(r)
>>0.000289 seconds (4 allocations: 160 bytes)
```




</font>

