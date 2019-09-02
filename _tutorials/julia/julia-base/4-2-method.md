---
youtube_id: 1YUtLYU9Pjk
qq_video_id: h083124n6l0
b_av: 61523729
b_cid: 106872795
b_page: 36
title: Julia方法
description: "方法和函数的区别？具体如何应用？"
chapter: 4
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 12
---


## 方法
**函数和方法的区别**

同样的函数，可以有不同的方法，比如加法函数，可以实现整数加法，浮点数加法和复数加法等，他们都是实现加法功能，即他们是同一个函数，但他们的实现方法不一样，可以理解位C++中的重载。
在REPL上可以看到我们定义的函数有几种方法。Julia会选择更加专用的那一个方法。
```Julia
function f9(x::Int64, y::Int64)
    x + y   
end
>>f9 (generic function with 1 method)
```
表示该函数只有一个方法

如果我们再定义一个f9，参数类型位Float64
```Julia
function f9(x::Float64,y::Float64)
    x + y   
end
>>f9 (generic function with 2 methods)
```
这时当我们执行`f(2,3)`编译器会自动选择更加专用的方法来执行，也就是第一个方法。

我们也可以直接输入`+`看到加号（也是一个函数）的方法数
```Julia
+
>>+ (generic function with 170 methods)
```
可以使用`methods(f9)`来看f9的具体方法



但如果两个方法都不更加专用，就会出现分歧。
```Julia
g(x::Int64, y) = 2x + y
g(x, y::Int64) = x + 2y
g(2,3.0)
>>7.0
g(2.0,3)
>>8.0
```
但如果是`g(2,3)`，那两个方法没有更加专用的，这时就会报error，解决方法就是定义一个更加专用的方法：`g(x::Int64, y::Int64)`。

**参数方法**

我们先定义一个简单的方法，对于任何输入，结果都是`false`
```Julia
f10(x,y) = false
f10(1,2)
>>false
```
再增加一个方法
```Julia
f10(x::T, y::T) where {T} = true
f10(1,2)
>>true
f10(1,2.1)
>>false
```
第二个方法的意思是当两个参数的类型相同时，返回true



当然，这种方式还可以针对矩阵进行操作
```Julia
f11(v::Vector{T}, x::T) where {T} = [v..., x]
f11([1,2,3],4)
>>4-element Array{Int64,1}:
 1
 2
 3
 4
f11([1,2,3],4.2)
>>ERROR: MethodError
```
还可以对子类型参数进行约束
```Julia
f12(x::T, y::T) where {T<:Number} = true
```






