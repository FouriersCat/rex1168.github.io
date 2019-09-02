---
youtube_id: dnXxfaTdZeM
qq_video_id: n0833p1uv9y
b_av: 61523729
b_cid: 106734274
b_page: 21
title: 避免拥有抽象类型参数的容器
description: "避免拥有抽象类型参数的容器"
chapter: 13
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 44
---



## 避免拥有抽象类型参数的容器

在定义struct时，我们经常会这样写

```
mutable struct MyType1
           x::AbstractFloat
       end
```
但用下面的写法会更好
```
mutable struct MyType2{T<:AbstractFloat}
           x::T
       end
```

查看他们的类型
```
a = MyType1(2.3)
typeof(a)
>>MyType1
b = MyType2(2.3)
typeof(b)
>>MyType2{Float64}
```

x的类型可以由b的类型决定，但不能由a的类型决定。b.x的类型不会改变，永远都是Float64，而a.x的类型则会改变。
```
typeof(a.x)
```

```
a.x = 2.3f0
typeof(a.x)
>>Float32

b.x = 2.3f0
typeof(b.x)
>>Float64
```

当某个包含多个变量的Array中，如果我们知道其中某个变量的类型，就明确指定出来
```
function foo(a::Array{Any,1})
    x = a[1]::Int32
    b = x+1
    ...
end
```

如果只能确定x的类型，但a[1]的类型不能确定，可以用convert()来完成
```
x = convert(Int32, a[1])::Int32
```

**从上面我们讲的这些内容也可以知道优化代码的一个策略：程序越简单越好，让编译器明确知道自己想干什么，而不是让编译器去猜我们的目的**

因此可以推断出有可变参数类型的函数肯定不如固定关键字参数类型的函数运行的快。

但这种指定某个变量类型在使用时需要注意一点，就是如果变量类型不是在编译时确定而是在运行时才确定，那会有损性能

```
function nr(n, prec)
    ctype = prec == 32 ? Float32 : Float64
    b = Complex{ctype}(2.3)
    d = 0
    for i in 1:n
        c = (b + 1.0f0)::Complex{ctype}
        d += c
    end
    d
end
```

那碰到这种需要在运行时才能确定类型的情况应该怎么处理才能使性能最佳？
```
function nr_op(n, prec)
    ctype = prec == 32 ? Float32 : Float64
    b = Complex{ctype}(2.3)
    d = opFunc(n, b::Complex{ctype})
end

function opFunc(n, b::Complex{T}) where {T}
    d = 0
    for i in 1:n
        c = (b + 1.0f0)::Complex{T}
        d += c
    end
    d
end
```

```
@time nr(10000, 64)
>>0.003382 seconds (20.00 k allocations: 625.188 KiB)

@time nr_op(10000, 64)
>>0.000023 seconds (7 allocations: 240 bytes)

```
我们将使用这种类型的变量的计算在函数值中完成，因为在函数调用时，会先确定输入参数的类型，这样在计算时就无需再去考虑类型的不确定问题了。如果不使用函数，则需要在每次赋值后再获取变量类型，再将结果转成对应类型。这样花费的时间会多很多。

简言之，**要先获取变量类型再计算**




