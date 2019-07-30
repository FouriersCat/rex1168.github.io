---
youtube_id: may2-QhKr6U
qq_video_id: w0833d15luz
b_av: 47775635
b_cid: 83687375
b_page: 43
title: 隐藏的类型转换
description: "隐藏的类型转换"
chapter: 13
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 43
---


## 隐藏的类型转换

在C++中，对每个定义的变量都有其固定的类型，但Julia中由于变量定义时可以缺省参数，经常会注意不到参数类型的转换。

```Julia
function cums(n)
    r = 0
    for i in 1:n
        r += cos(i)
    end
    r
end

function cums_f(n)
    r = 0.0
    for i in 1:n
        r += cos(i)
    end
    r
end
```
把两个函数放在一起时，有过编程基础的同学可以很容易看出，第一个函数中r是个Int64类型的，程序运行时需要把Int64转成Float64再进行加法计算。但如果没有把这两个函数放在一起，会不会被很多同学忽略掉？

对比两个函数的类型warning

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-18.png)

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-19.png)


对比两个函数的运行时间

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/13-20.png)

因此我们在定义变量时，要尽量保持与其后面运算时的类型一致。有几个函数可以帮助我们完成这种定义。

 - zero(value)
 - eltype(array)
 - one(value)
 - similar(array)

我们在定义变量时，可以使用这4个函数来避免常范的错误。

eg.
```
pos(x) = x < 0 ? zero(x) : x
```

eg.求一个array的和
```Julia
x = [1.0, 2.1, 4.5]
sm = zero(x[1])
sm = sum(x)    # 当然可以不用定义sm，直接写这一句，这只是个例子
```

其他三个的用法基本相同，这里不再举例。




</font>

