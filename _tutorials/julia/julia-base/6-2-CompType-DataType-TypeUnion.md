---
youtube_id: lNc1YHboo3M
qq_video_id: p0833do6jpc
b_av: 61523729
b_cid: 106937703
b_page: 42
title: 复合类型 可变复合类型 DataType TypeUnion
description: "复合类型 可变复合类型 DataType TypeUnion"
chapter: 6
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 18
---




## 复合类型

即自定义类型，关键字是`struct`,Julia中没有class关键字，都用struct代替
```Julia
struct Foo
    x1
    x2::Int
    x3::Float64
end
foo = Foo("Hello World!", 10, 11.9)
typeof(foo)
>>Foo
foo.x1
>>"Hello World!"
```

在foo的创建过程中，有两个默认的构造函数会被自动生成，一个可以接受任意参数，如上面的x1，另接受与字段类型完全匹配的参数，如上面的x2,x3。

struct是不可变类型，foo在创建后，内容不可更改。

```Julia
foo.x2 = 2
>>type Foo is immutable

Stacktrace:
 [1] setproperty!(::Foo, ::Symbol, ::Int64) at .\sysimg.jl:19
 [2] top-level scope at In[15]:1
```




## 可变复合类型
```Julia
mutable struct Foo
    x1
    x2::Int
    x3::Float64
end
foo2 = Foo("Hello World", 10, 12.1)
foo2.x2 = 20
```
可变的复合类型是建立在堆上的，并具有稳定的内存地址。



## DataType


我们上面讨论的抽象类型、原始类型和复合类型，都有以下的共性：
 - 它们都是显式声明的。
 - 它们都具有名称。
 - 它们都已经显式声明超类型。
 - 它们可以有参数。

```Julia
typeof(Real)
>>DataType
```
DataType 可以是抽象的或具体的。它如果是具体的，就具有指定的大小、存储布局和字段名称（可选）。因此，原始类型是具有非零大小的 DataType，但没有字段名称。复合类型是具有字段名称或者为空（大小为零）的 DataType。

每一个具体的值在系统里都是某个 DataType 的实例。




## Type Unions


类型共用体是一种特殊的抽象类型，具体用法：
```Julia
IntOrString = Union{Int,AbstractString}
123::IntOrString
>>123
"abc":IntOrString
>>"abc"
12.4::IntOrString
>>TypeError: in typeassert, expected Union{Int64, AbstractString}, got Float64

f(x::IntOrString) = println(x)
f(12)
f("abc")
f(23.4)
f('a')
```





