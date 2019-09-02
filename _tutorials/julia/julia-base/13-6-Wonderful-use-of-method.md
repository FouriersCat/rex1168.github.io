---
youtube_id: 0EvDpla3lT0
qq_video_id: k08339vs9i9
b_av: 61523729
b_cid: 106740105
b_page: 22
title: 参数类型优化
description: "参数类型优化"
chapter: 13
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 45
---

## 参数类型优化


当我们的类型是函数参数时，也要用上面的方式先获取

```
# 定义一个N维矩阵
function array3(fillval, N)
           fill(fillval, ntuple(d->3, N))
       end
```
```
typeof(array3(2.0, 4))
>>Array{Float64,4}
```

这里的N是矩阵的类型参数，该矩阵是一个N维的。同样存在的问题是，该矩阵变量的类型参数就是N的值，如果我们先获取了N的值后再进行矩阵生成，性能会更好。

```
function array3(fillval, ::Val{N}) where N
           fill(fillval, ntuple(d->3, Val(N)))
       end
```
在使用Val()时，一定要注意使用方式，如果使用不当，则比不使用Val()性能更差

```
function call_array3(fillval, n)
    A = array3(fillval, Val(n))
end
```

这样编译器就不知道n是个什么东西，也不知道n的类型，跟我们的初衷南辕北辙。


我们再举个可变参数和关键字参数的例子说明两者的运行时间的差异

```
function func1(n, x...)
    res = 0
    for i in 1:n
        res += x[2]
    end
    res
end

function func2(n, x, y)
    res = 0
    for i in 1:n
        res += y
    end
    res
end
```

```
@time func1(1000000000, 10, 10)
>> 0.408809 seconds (5 allocations: 176 bytes)

@time func2(1000000000, 10, 10)
>> 0.000003 seconds (5 allocations: 176 bytes)
```

把参数明确后，运行速度快了好几个数量级。

## 用方法代替函数中的判断条件

```
using LinearAlgebra

function mynorm(A)
    if isa(A, Vector)
        return sqrt(real(dot(A,A)))
    elseif isa(A, Matrix)
        return maximum(svdvals(A))
    else
        error("mynorm: invalid argument")
    end
end
```

函数mynorm如果写成下面的形式效率会更高一些

```
norm(x::Vector) = sqrt(real(dot(x, x)))
norm(A::Matrix) = maximum(svdvals(A))
```



