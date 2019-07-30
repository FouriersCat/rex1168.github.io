---
youtube_id: Q_RrW9NNR4E
qq_video_id: u0833hdonur
b_av: 47775635
b_cid: 83686868
b_page: 40
title: 避免全局变量
description: "避免全局变量"
chapter: 13
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 40
---


## 避免全局变量

全局变量的值和类型随时都会发生变化。 这使编译器难以优化使用全局变量的代码。 变量应该是局部的，或者尽可能作为参数传递给函数。

**任何注重性能或者需要测试性能的代码都应该被放置在函数之中。**

把全局变量声明为常量可以巨大的提升性能。

```Julia
const VAL = 0
```

如果必须要声明全局变量，可以在使用它的地方标注他们的类型来优化效率。

```
global x = rand(10000)

function lp1()
    s = 0.0
    for i in x
        s += i
    end
end

```

使用`@time`来估算代码运行时间

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-1.png)

在函数第一次运行时，由于jit的原因，需要预热，第二次再运行的结果是真正的代码运行时间。

**以下的`@time`结果都是不包含jit的时间。**

在图中，我们不仅可以知道代码运行时间，还可以看出代码占用的内存大小。从内存大小上，我们也可以大概看出程序是不是存在问题。


比如函数`lp1()`申请了733KB的内存，仅仅是计算64位浮点数加法，说明肯定是有多余的空间可以节省的。

下面我们用传递函数参数和指定变量类型的方式运行
```
function lp2()
    s = 0.0
    for i in x::Vector{Float64}
        s += i
    end
end

function lp3(x)
    s = 0.0
    for i in x
        s += i
    end
end
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-2.png)

可以看出，运行时间大为减少，但还是占用了160Byte的内存，这是由于在全局作用域中运行`@time`导致的。

如果我们把测试代码放置到函数之中，就不存在这个问题。

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-3.png)

***对于更加正式的性能测试，可以使用BenchmarkTools.jl包，这个包会多次评估函数的性能以降低噪声。***

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-4.png)

可以看出，指定变量类型的方式是最快的。



