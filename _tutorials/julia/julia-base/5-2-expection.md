---
youtube_id: ZlcciangAjE
qq_video_id: w08338wu12s
b_av: 61523729
b_cid: 106899689
b_page: 39
title: Julia异常处理
description: "Julia中碰到异常怎么办？"
chapter: 5
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 15
---

**异常处理**
```Julia
sqrt(-1)
>>DomainError with -1.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).

Stacktrace:
 [1] throw_complex_domainerror(::Symbol, ::Float64) at .\math.jl:31
 [2] sqrt at .\math.jl:479 [inlined]
 [3] sqrt(::Int64) at .\math.jl:505
 [4] top-level scope at In[4]:1
```
```Julia
try
    sqrt(-1)
catch
    println("pass")
end

sqrt_second(x) = try
           sqrt(x[2])
       catch y
           if isa(y, DomainError)
               sqrt(complex(x[2], 0))
           elseif isa(y, BoundsError)
               sqrt(x)
           end
       end
```
finally语句
通常异常会导致程序提前退出，使得某些操作不能执行，关键字 finally 可以解决这样的问题，无论程序是怎样退出的，finally 语句总是会被执行。
```Julia
f = open("file")
try
    # operate on file f
finally
    close(f)
end
```
可以将`try`、`catch`和`finally`连起来使用，`finally`在`catch`处理完异常后执行。








