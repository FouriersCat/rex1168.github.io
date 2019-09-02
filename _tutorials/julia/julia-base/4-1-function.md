---
youtube_id: WGWdMz6B5Ro
qq_video_id: k08310x7k6g
b_av: 61523729
b_cid: 106851605
b_page: 35
title: Julia函数
description: "函数的各种用法"
chapter: 4
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 11
---

## 函数

**函数定义**
```Julia
function f1(x,y)
    x + y   # 可以不带return,当然也可以写成return x + y
end
>>f (generic function with 1 method)
f(1,2)
>>3
```
函数的最后一行是不需要加return的，return一般用于在函数中间返回时使用。因为Julia的代码都是表达式（在后面的元编程一节中会讲到），表达式是有返回值的，要么是`nothing`，要么是别的，因此函数最后一行默认就是返回值，无需再加`return`;如果一个函数不想有返回值，那再最后一行写个`nothing`即可。


可以指定函数输出的类型
```Julia
function g(x, y)::Int8
           return x * y
end
typeof(g(2,3))
>>Int8
```
符号也可以当做函数使用
```Julia
+(1,2,3)
>>6
f2 = +
f2(1,2,3)
>>6
```
给函数添加说明
```Julia
"simple add function"
function funcAdd(x, y)
    x + y
end
```
使用`@doc funcAdd`可以看到函数前的注释，如果是在REPL中定义的函数，则可以在`help`模式下查看函数使用说明








**匿名函数**
```Julia
map(x->x*2 + 1, [1,2,3,4])
>>4-element Array{Int64,1}:
 3
 5
 7
 9
```








**多返回值**
```Julia
function f3(x,y)
    x+y, x-y
end
f3(3,4)    
>>(7,-1)  #返回的是一个tuple
minVal(x,y) = (x < y) ? x : y;
```






**可变参数**
```Julia
function f4(x...)
    r1 = length(x)
    r2 = x[r1]
    return r1,r2
end
println(f4(4,6,9))
>>(3,9)
println(f(11,15,(18,20)))
>>(3, (18,20))
```


**默认参数 关键字参数**
```Julia
function f5(x,y=1)
   return  x + y
end
f5(3)
f5(5,6)
f5(x=7,y=8)

function f6(x;y,z=3)
   return  x + y + z
end
f6(3,y=5)
f6(4,y=3,z=8)
```

**参数类型**
Julia虽然属于动态语言，但函数定义时可指定其参数类型
```Julia
function f7(x::Int8)
    x + 3
end
f7(Int8(10))
```

函数的其他使用方法

**map**
```Julia
function funcAbs(x)
    x >=0 ? x : -x
end
map(funcAbs, -3:3)
```

**reduce**
```JUlia
fucntion add(x, y)
    x + y
end
reduce(add, 1:10)
>>55
reduce(+, 1:10)
>>55
```

`|>`操作符，对于运算顺序非常直观
```Julia
"abcde" |> uppercase
[i for i in 1:5] |> join
```


当然，Julia中函数还有更简单灵活的定义方式
```Julia
f8(x::Int64,y::Int64) = 2*x + y
f8(4,3)
>>11
```
看到这里，是不是更加喜欢Julia了！它就是这么简洁明了。






