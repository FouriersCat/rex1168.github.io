---
youtube_id: D125TQ_Zpnc
qq_video_id: v0833ammsoc
b_av: 61523729
b_cid: 106913646
b_page: 41
title: 类型声明 抽象类型 原始类型
description: "类型声明 抽象类型 原始类型"
chapter: 6
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 17
---

# 类型
Julia中没有class，也没有子类型的继承关系，**所有具体类型都是最终的**，并且只有抽象类型可以作为其超类型。Julia中的继承是继承行为，而不是继承结构。



## 类型声明

声明某个变量的类型，也可以用来断言变量类型是否正确
```Julia
(2+4)::Float64
>> ERROR: ...
(2+4)::Int64
6
```
类型声明常用的两个地方在函数中的参数类型和返回类型
```Julia
function f9(x::Int64)::Float64
    x/10
end
f9(10)
>>1.0
```




## 抽象类型

 抽象类型不能被实例化，我们前面讲到的Int64/Float64等都是抽象类型的具体类型，抽象类型不能直接使用，类似于C++中的抽象类。
 抽象类型的定义方法如下：
 ```Julia
 abstract type «name» end
 abstract type «name» <: «supertype» end
 ```
第二行中的`<:`表示新声明的抽象类型是后面类型的子类型。
如果没有给出父类型，则默认为Any。
抽象类型有：
```Julia
abstract type Number end
abstract type Real     <: Number end
abstract type AbstractFloat <: Real end
abstract type Integer  <: Real end
abstract type Signed   <: Integer end
abstract type Unsigned <: Integer end
```

```Julia
Integer <: Number
>>true
Integer <: AbstractFloat
>>false
```





## 原始类型

原始类型是具体类型，其数据是由简单的位组成。即我们前来讲到的Float64/Int64/Uint64/Uint32等。是可以实例化为对象的。
声明原始类型的语法为：
```Julia
primitive type «name» «bits» end
primitive type «name» <: «supertype» «bits» end
```
而标准的原始类型都是在语言本身中定义的：
```Julia
primitive type Float16 <: AbstractFloat 16 end
primitive type Float32 <: AbstractFloat 32 end
primitive type Float64 <: AbstractFloat 64 end
primitive type Bool <: Integer 8 end
primitive type Char <: AbstractChar 32 end
primitive type Int8    <: Signed   8 end
primitive type UInt8   <: Unsigned 8 end
```

原始类型的应用
```Julia
function f10(x::Int64)::Int32
    x + 10
end
a = f10(10)
```
我们前面说到抽象类型不能被实例化，但如果我们把上面的Int64换成抽象类型Real，发现也是可以正确被赋值的
```Julia
function f11(x::Real)::Real
    x + 10
end
b = f11(10)
```
这又是为什么呢？我们可以用`typeof()`函数查看变量的类型
```Julia
typeof(a)
>>Int32
typeof(b)
>>Int64
```
即在使用抽象类型时，Julia会针对每个调用它的参数的具体类型重新编译。
有两点需要说明：
 - 即使我们的参数类型为抽象类型，性能不会有任何损失；但如果函数参数是抽象类型的容器（比如数组，矩阵等），可能存在性能问题
 - 我们前面讲到的Bool、UInt8和Int8都是8 bits，但它们的根本区别是具有不同的超类型：Bool的超类型是Integer，Int8是Signed而UInt8是Unsigned。


### 类型转换

这个我们在前面讲函数时其实已经提到过
```Julia
function foo(a, b)
    x::Int8 = a
    y::Int8 = b
    x + y
end
```
该函数中把Int64类型转成了Int8后再进行计算。也可以像C++中的强制类型转换的用法一样
```Julia
Int8(10)
convert(Int8, 10)
```
但这种强转仅限于数字之间，而且不能越界
```Julia
Int8(1000)
>>error
Int8("10")
>>error
convert(Int8, "10")
>>error
```
如果我们就是想把字符串转成数字的话，可以用`parse`函数
```Julia
parse(Int8, "10")
```
我们也可以为`convert()`函数增加一个方法
```Julia
Base.convert(::Type{Int8}, x::String) = parse(Int8, x)
convert(Int8, "10")
```

### 类型提升

先看一个简单的例子
```julia
1 + 2.0
>>3.0
```
这个例子中，就是把Int64类型的1转换成了Float64，然后进行加法操作

再来看下面的例子
```
a = (1, 2.0, Int8(3))
>>(1, 2.0, 3)
b = promote(1, 2.0, Int8(3))
>>(1.0, 2.0, 3.0)
```
使用promote后，Tuple中的所有元素都提升到了Float64类型

```
+(1, 2.0)
>>3.0
a = (1, 2.0)
+(a...)
>>3.0
```
其实`+(1，2.0)`就相当于`+(promote(1, 2.0)...)`,可以运行`@edit +(1+2.0)`查看Julia的实现方式。






