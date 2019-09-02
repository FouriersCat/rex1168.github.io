---
youtube_id: giZUQPvN45U
qq_video_id: k0833xz4ijj
b_av: 61523729
b_cid: 106721642
b_page: 19
title: 抽象类型 具体类型
description: "抽象类型 具体类型"
chapter: 13
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 42
---

## 抽象和具体类型

当我们定义一个函数时，如果函数参数的类型是固定的，比如是一个Int64的Array[1,2,3,4],那他们在内存中会连续存放；

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-12.png)


但如果函数参数的类型是Any，那么内存中连续存放只是他们的“指针”,会指向其实际的位置。这样一来，数据存取就慢下来了。

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-13.png)

看下面的例子。
```Julia
import Base: +, *
struct IntNum
    n::Int64
end

struct AnyNum
    n::Number
end

+(a::IntNum, b::IntNum) = IntNum(a.n + b.n)
*(a::IntNum, b::IntNum) = IntNum(a.n * b.n)

+(a::AnyNum, b::AnyNum) = AnyNum(a.n + b.n)
*(a::AnyNum, b::AnyNum) = AnyNum(a.n * b.n)
```

可以用`@code_native +(IntNum(1), IntNum(2))`和`@code_native +(AnyNum(1), AnyNum(2))`来对比两个函数的机器语言的长度，可以明显看出AnyNum的操作要比IntNum多很多操作。

```Julia
function calc_Int_time(a,b,c,n)
    for i in 1:n
        cal(IntNum(a), IntNum(b), IntNum(c))
    end
end

function calc_Any_time(a,b,c,n)
    for i in 1:n
        cal(AnyNum(a), AnyNum(b), AnyNum(c))
    end
end
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-14.png)

从程序运行时所占的内存大小也可以看出IntNum要比AnyNum少很多。
所以，计算concrete类型会比计算abstract类型要节省时间。

我们可以使用`@code_warntype`来查看运行的函数中是否有abstract类型

对于concrete类型：

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-15.png)

对于abstract类型：

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-16.png)

对于有abstract类型的地方，会用红色的标出。

再举一个Julia自带函数的例子。

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-17.png)




