---
youtube_id: DMrKXKMb1qY
qq_video_id: c083306xpdw
b_av: 61523729
b_cid: 106742423
b_page: 23
title: 矩阵运算优化
description: "矩阵运算优化"
chapter: 13
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 46
---

## 矩阵优化

在Julia中，多维矩阵是以列优先原则排列，这跟MATLAB中是一样的

```
x = [1 2; 3 4]

# 把x转换为1维矩阵
x[:]
```

也就是说，Julia中矩阵的每一列的数据在内存上的地址是连续的，每一行的地址不是连续的，操作连续地址比非连续地址速度要快很多。下面举一个矩阵拷贝的例子。

```
# 按列拷贝
function copy_cols(x::Vector{T}) where T
    inds = axes(x, 1)
    out = similar(Array{T}, inds, inds)
    for i = inds
        out[:, i] = x
    end
    return out
end

# 按行拷贝
function copy_rows(x::Vector{T}) where T
    inds = axes(x, 1)
    out = similar(Array{T}, inds, inds)
    for i = inds
        out[i, :] = x
    end
    return out
end

# 按元素拷贝，以列为顺序
function copy_col_row(x::Vector{T}) where T
    inds = axes(x, 1)
    out = similar(Array{T}, inds, inds)
    for col = inds, row = inds
        out[row, col] = x[row]
    end
    return out
end

# 按元素拷贝，以行为顺序
function copy_row_col(x::Vector{T}) where T
    inds = axes(x, 1)
    out = similar(Array{T}, inds, inds)
    for row = inds, col = inds
        out[row, col] = x[col]
    end
    return out
end
```

```
x = randn(10000)
fmt(f) = println(rpad(string(f)*": ", 14, ' '), @elapsed f(x))
map(fmt, Any[copy_cols, copy_rows, copy_col_row, copy_row_col])
>>copy_cols:    0.205115039
 copy_rows:     0.856341406
 copy_col_row:  0.279589601
 copy_row_col:  0.845328085
```

**dot运算**

在矩阵操作中，操作符前加上.，看下面的例子

```
f(x) = 5x.^3 - 2x.^2 + 3x
fdot(x) = @. 5x^3 - 2x^2 + 3x
```
```
x = rand(10^6);
@time f(x);
>>0.040295 seconds (20 allocations: 53.407 MiB, 17.06% gc time)

@time fdot(x);
>>0.002195 seconds (8 allocations: 7.630 MiB)

@time f.(x);
0.002047 seconds (8 allocations: 7.630 MiB)
```

可以看出，点乘操作要快很多。

**向量化并不会提高Julia的运行速度**

很多用过MATLAB和Python的同学都会觉得向量操作肯定要比循环操作要快很多，但在Julia中并没有这个规则，这一点要由为注意。

```
function loop_sum()
    s = 0.0
    for i = 1:1000
        s = 0.0
        for k = 1:10000
            s += 1.0/(k*k)
        end
    end
    s
end
```

先用`@code_warntype loop_sum()`看一下代码有没有类型转换问题。

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-21.png)

运算过程中不存在类型转换的warning。

```
@benchmark loop_sum()
>>BenchmarkTools.Trial: 
  memory estimate:  0 bytes
  allocs estimate:  0
  --------------
  minimum time:     8.868 ms (0.00% GC)
  median time:      8.869 ms (0.00% GC)
  mean time:        8.870 ms (0.00% GC)
  maximum time:     8.925 ms (0.00% GC)
  --------------
  samples:          564
  evals/sample:     1
```

用向量来完成上面的累加操作
```
function vec_sum()
    s = 0.0
    k = collect(1:10000)
    for i=1:1000
        s = sum(1 ./(k.^2))
    end
end

@benchmark vec_sum()
>>BenchmarkTools.Trial: 
  memory estimate:  76.48 MiB
  allocs estimate:  3002
  --------------
  minimum time:     19.575 ms (17.75% GC)
  median time:      22.603 ms (21.13% GC)
  mean time:        23.038 ms (22.79% GC)
  maximum time:     59.079 ms (68.95% GC)
  --------------
  samples:          217
  evals/sample:     1
```

@simd 可以在运算支持被重新recorded时加速运算，加法操作就是可以被加速的
```
function loop_sum_simd()
    s = 0.0
    for i = 1:1000
        s = 0.0
        @simd for k = 1:10000
            s += 1.0/(k*k)
        end
    end
    s
end

@benchmark loop_sum_simd()
>>BenchmarkTools.Trial: 
  memory estimate:  0 bytes
  allocs estimate:  0
  --------------
  minimum time:     6.202 ms (0.00% GC)
  median time:      6.236 ms (0.00% GC)
  mean time:        6.240 ms (0.00% GC)
  maximum time:     7.119 ms (0.00% GC)
  --------------
  samples:          802
  evals/sample:     1
```

说明Julia中向量运算并不会优化速度，这一点在Julia官网也多次说明。




