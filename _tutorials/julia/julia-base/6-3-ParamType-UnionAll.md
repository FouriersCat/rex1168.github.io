---
youtube_id: __iBJ8cGJWk
qq_video_id: y0833mj4d5p
b_av: 47775635
b_cid: 83683211
b_page: 19
title: 参数类型 UnionAll
description: "参数类型 UnionAll"
chapter: 6
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 19
---





## 参数类型

Julia类型系统的一个重要特色就是类型可以支持参数化，我们前面讲到的原始类型、抽象类型和复合类型都支持参数化。类似于C++中的template，但Julia是一种动态语言，在使用参数类型方面优势更加明显。

 1. 复合参数类型
 
```Julia
struct Pos{T}
    x::T
    y::T
end
a = Pos{Int64}(1,2)
b = Pos{Float64}(1.1,2.2)
c = Pos(3,4)
d = Pos(3.1,4.2)
typeof(c)
>>Pos{Int64}
typeof(d)
>>Pos{Float64}
```
每个实例都是Pos的子类型
```Julia
Pos{Float64} <: Pos
>>true
Pos{Int64} <: Pos
>>true
```
但不同的T声明的具体类型之间不能互为子类型
```Julia
Pos{Float64} <: Pos{Real}
>>false
```
还有一点需要注意，虽然有`FLoat64<:Real`，但没有`Pos{Float64}<:Pos{Real}`.
他们的关系如下


![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%B1%BB%E5%9E%8B.png)



所以，下面的定义就不能使用
```Julia
function norm(p::Pos{Real})
    sqrt(p.x^2 + p.y^2)
end
p1 = Pos{Real}(1,2)
norm(p1)
>>2.23606797749979
p2 = Pos{Int64}(1,2)
norm(p2)
>>error
```

但我们可以写成这样
```Julia
function nrom2(p::Pos{<:Real})
    sqrt(p.x^2 + p.y^2)
end
norm(p2)
>>2.23606797749979
```

复合参数类型也支持多个参数类型
```Julia
struct Pos1{T1,T2}
    x1::T1
    x2::T2
end
p1 = Pos1{Int64,Float64}(1,2.2)
p1.x1
>>1
p1.x2
>>2.2
```

 2. 抽象参数类型

由抽象类型而来，顾名思义，就是给抽象类型加了个参数
```Julia
abstract type Pointy{T} end 
```
与复合参数类型一样，每个实例都是Pointy的子类型
```Julia
Pointy{Int64} <: Pointy
>>true
```
不同的T之间不能互为子类型
```Julia
Pointy{Float64} <: Pointy{Real}
>>false
```

 3. 原始参数类型

由原始类型而来，就是给原始类型加了个参数
```Julia
primitive type Ptr{T} 64 end
Ptr{Float64} <: Ptr
>>true
```

 4. 元组类型

元组的定义我们再《变量》一节中讲过了，元组的内容不可更改
```Julia
t1 = (1, 2.8)
```
就相当于不可变参数类型的struct
```Julia
struct Tuple2{T1,T2}
    x1::T1
    x2::T2
end
t2 = Tuple2{Int64,Float64}(1, 2.8)
```
那它们之间有什么区别么？
 - 元组类型可以具有任意数量的参数。
 - 元组类型的参数是协变的：Tuple{Int} 是 Tuple{Any}的子类型。因此，Tuple{Any}被认为是一种抽象类型，且元组类型只有在它们的参数都是具体类型时才是具体类型。
 - 元组没有字段名称; 字段只能通过索引访问。

## 变参元组类型

元组类型的最后一个参数可以是特殊类型`Vararg`，表示后面可跟任意多个参数
```Julia
Tup = Tuple{Float64, Vararg{Int64}}
isa((2.2,), Tup)
>>true
isa((2.2, 3), Tup)
>>true
ias((2.2, 3.1), Tup)
>>false
ias((2.2, 3, 4), Tup)
>>true
```

## 单态类型

所谓的单态类型，就是`Type{T}`，它是个抽象类型，且唯一的实例就是对象T。
```Julia
isa(Int64, Type{Int64})
>>true
isa(Real, Type{Int64})
>>flase
isa(Type{Int64}, Type)
>>true
isa(Type{Real}, Type)
>>true
```

## UnionAll类型

在抽象参数类型中，我们讲到，`Pointy`是它所有实例`Pointy{T})`等的超类型，但T的类型是不确定的，那这又是如何工作的呢？这就引入了UnionAll类型，Pointy是一种UnionAll类型，是这种类型某些参数的所有值的类型的迭代并集。

UnionAll类型通常使用关键字`where`编写
```Julia
struct Pos{T}
    x::T
    y::T
end
function f1(p::Pos{T} where T<:Real)
    p
end
```
当有两个类型参数时
```
struct Pos2{T1,T2}
    x::T1
    y::T2
end
function f2(p::Pos2{T1,T2} where T1<:Int64 where T2<:Float64)
    p
end
```

where还可以限定T的上下界
```
function f23(Pos{T} where Int64<:T<:Real)
    p
end
```


