---
youtube_id: PA4aFm53TJY
qq_video_id: x08333dazkx
b_av: 61523729
b_cid: 106889342
b_page: 38
title: Julia控制流
description: "Julia中都有哪些控制流？如何使用？"
chapter: 5
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 14
---


# 控制
Julia中提供的控制流
 - 复合表达式 ： begin 和 (;)
 - 条件求值 ： if-elseif-else 和 ?: (ternary operator)
 - 短路求值 ： &&, || 和 chained comparisons
 - 重复求值: 循环 ： while 和 for
 - 异常处理 ： try-catch ， error 和 throw
 - 任务（也称为协程） ： yieldto

**复合表达式**

*begin块*
```Julia
z = begin
     x = 1
     y = 2
     x + y
    end
>>3
```
*`(;)`链式*
```Julia
z = (x = 1; y = 2; x + y)
```

这两种表示方式的值，都是最后一个表达式的值。

**条件极值**
```Julia
if x < y
    println("x is less than y")
elseif x > y
    println("x is greater than y")
else
    println("x is equal to y")
end
```
判断的条件必须是`true`或`false`中的一个
```Julia
if 1
    println("test...")
end
>>TypeError: non-boolean (Int64) used in boolean context
```

while循环
```Julia
i = 5
while i <= 5
         println(i)
         i += 1
       end
```
上面的循环用for循环写为
```Julia
for i = 1:5
    println(i)
end
```
for还可以遍历任意容器
```Julia
for i in [1,2,3,4,5]
    println(i)
end
```
for的其他用法
```Julia
for i in "abcd"
    println(i)
end

for i in 1:2:10
    println(i)
end

for i in 10:-2:1
    println(i)
end

[x^2 for x in 1:4]
[x^2 for x ∈ 1:5]
[(x, x^2) for x ∈ 1:5]

for (i,x) in enumerate(1:4:20)
    println(i, " ", x)
end

collect(enumerate(1:4:20))

```
continue和break的用法跟其他语言基本一致，这里就不多讲了。



**变量作用域**
```Julia
for i in 1:5
    x = i
end
```
此时如果在for循环外面查看x的值，则会提示error，因为变量x只是for循环内部的。

如果想在for外部也使用x，则要写成
```Julia
for i in 1:5
    global x = i
end
```

如果我们在外部提前定义了x
```Julia
x = 10
for i in 1:5
    x = i
end
```
此时的x仍为10，因为for里面默认x是local的






