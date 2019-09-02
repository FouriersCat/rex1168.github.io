---
youtube_id: s0F9Vk2AJr4
qq_video_id: q0833eew21b
b_av: 61523729
b_cid: 106993362
b_page: 47
title: Julia模块
description: "模块相当于Python中的库，那应该怎么导入模块，怎么自定义模块？"
chapter: 9
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 23
---



# 模块

模块是一些互相隔离的工作空间，用法上类似于Python中的库，在Python中导入库时，使用`import * as *`的方式，在Julia中，采用using或import导入要使用的模块

我们的模块内容如下
```Julia
module MyModule

export my_square, my_abs

# square function
my_square(x::Int64) = x * x

# abs function
my_abs(x) = (x>=0) ? x : -x

# add function 
my_add(x,y) = x + y

# minus function 
my_minus(x,y) = x - y

# multiply function
my_multiply(x,y) = x * y

end
```
























在REPL中或者vscode(Atom)中运行`using MyModule`时，会提示错误如下

REPL中提示错误


![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/%E6%A8%A1%E5%9D%971.png)

















vscode中提示错误


![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/%E6%A8%A1%E5%9D%972.png)





这是因为Julia中并未找到该Module的位置，我们可以手动添加进来，在程序的最开始，加上`push!(LOAD_PATH, ".")`，`.`表示当前工作目录，也可以把`.`换成绝对目录;也可以先用`cd dir`的命令切换到我们的Module的目录，再直接使用`push!(LOAD_PATH, ".")`，这样就可以正确的调用MyModule了。

如果不想在程序中写出来，也可以在`~/.julia/config/startup.jl`文件中写明，该文件指明了程序运行时需要执行哪些操作。在Windows中，`~`指的是`C:\User\UserName`目录；MAC的`~`指的是`User\UserName`目录。

MyModule中的`export`是将这两个函数导出来，这样就可以直接使用`my_square`和`my_abs`函数，而不必非要使用`MyModule.my_square`和`MyModule.my_abs`
调用模块的代码如下

```Julia
using MyModule
using MyModule:my_add, my_multiply
res1 = MyModule.my_square(2)
res2 = my_square(3)
res3 = my_abs(-4)
res5 = my_add(3,4)  
res6 = MyModule.my_minus(4,3)
res7 = my_minus(4,3) # error
```

我们还可以使用`import MyModule`语句，基本用法相同，如果申明了`export`就可以不用加模块名，没声明的话要加模块名，不同的地方在于如果是函数的话，`using`不允许给他们增加新的方法，只能使用他们，而`import`不仅可以使用，还可以为其增加新的方法。

```Julia
import MyModule
my_square(x::Float64) = x * x
res = my_square(2.3)
println(res)
```









**模块和文件**


模块和文件并没有关系，一个模块可以有多个文件，一个文件也可以有多个模块

一个模块多个文件
```Julia
module MyModu

include("file1.jl")
include("file2.jl")

end
```
一个文件多个模块
```Julia
module Test1
include("file1.jl")
end

module Test2
include("file2.jl")
end
```








**标准模块**

有三个非常重要的标准模块：Main，Core 和 Base

Main 是最顶层的模块，Julia 启动后会将 Main 设置为当前模块。在提示符下定义的变量会进入到 Main，执行 varinfo() 会列出 Main 中的变量。

Core 包含所有语言内置的标识符（语言的核心部分，不是库），每个模块都默认声明了 using Core（否则的话啥也做不了）。

Base 模块包含了一些基本的功能（即源码中 base/ 目录下的内容）。所有模块都默认包含了 using Base，因为对大多数库来说，都会用到。




