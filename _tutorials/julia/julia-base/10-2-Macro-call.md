---
youtube_id: LKSxXcYa9O8
qq_video_id: a0833c5qtfk
b_av: 47775635
b_cid: 83684955
b_page: 25
title: Julia宏调用
description: "宏这个概念在其他语言中都存在，在Julia中宏应该怎么用呢？"
chapter: 10
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 25
---


### 宏调用

前面我们也调用Macro，比如`@ILike("Julia")`，但宏调用的语法是很有讲究的，下面我们来具体看一下。
两种常用的宏调用方式
```Julia
@name expr1 expr2 ...
@name(expr1, expr2, ...)
```
**需要注意的是：第一种用法两个参数之间是用空格隔开，且参数之间没有逗号；第二种用法的name和()之间是没有空格的，且参数之间有逗号隔开。**

如果写成了
```Julia
@name (expr1, expr2, ...)
```
则表示参数为一个tuple。

当调用array时，可采用如下的方式
```
@name[a b] * v
@name([a b]) * v
```
对于Macro的参数，我们可以用`show`打印出来
```
macro showarg(x)
    show(x)
end
@showarg(a)
>>:a
@showarg(1+1)
>>:(1 + 1)
```
每个Macro都会被传入两个额外的参数：`__source__`和`__module__`，他们可以指示出location，在调试的时候很有用处。

由于Julia是多重派发的，Macro也支持类似于函数一样的多种方法。下面我们来看一个用Macro调试的例子。
```Julia

debugging = true

macro debug1(msg...)
    if debugging
        :(println("DEBUG> ", $(msg...)))
    else
        :nothing
    end
end

macro debug1(lineno, msg...)
   if debugging
        :(println("DEBUG ", basename(@__FILE__),":",$lineno, "> ", $(msg...)))
    else
        :nothing
    end
end

function bubble_sort!(xs::Vector)
    println("bubble_sort starting...")
    n = length(xs)
    swapped = true
    while swapped
        swapped = false
        for i in 1:n-1
            if xs[i] > xs[i+1]
                # println(xs)
                xs[i+1], xs[i] = xs[i], xs[i+1]
                @debug1(@__LINE__, join(xs, " "))
                swapped = true
            end
        end
    end
    xs
end

xs = Vector([1, 3, 4, 5, 8, 2, 7])
res = bubble_sort!(xs)
```
运行结果为
```
bubble_sort starting...
DEBUG bubble_sort.jl:34> 1 3 4 5 2 8 7
DEBUG bubble_sort.jl:34> 1 3 4 5 2 7 8
DEBUG bubble_sort.jl:34> 1 3 4 2 5 7 8
DEBUG bubble_sort.jl:34> 1 3 2 4 5 7 8
DEBUG bubble_sort.jl:34> 1 2 3 4 5 7 8
```


