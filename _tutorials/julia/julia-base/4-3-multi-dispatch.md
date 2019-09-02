---
youtube_id: H14rsP644mM
qq_video_id: z0831eiuwgn
b_av: 61523729
b_cid: 106882188
b_page: 37
title: Julia多重分派
description: "什么是多重分派？如何在Julia中使用单分派"
chapter: 4
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 13
---



## 多重分派

分派就是指根据变量的类型选择相应的方法，单分派指的是指根据第一个参数类型去选择方法。

下面我们举一个Python中的例子，Python因为在函数定义时是不知道参数类型的，所以一般没有单分派；但Python中提供了单分派的修饰符，可以实现单分派的功能。
```Python
from functools import singledispatch

@singledispatch
def func(arg, verbose=False):
    print('initial...\n')

@func.register(int)
def _(arg, verbose=False):
    print(arg)

func(1)
>>1
func(2.3)
>>initial...
```

可以看出，函数func()的结果只跟第一个参数的类型有关，跟后面的参数没有关系，这就是单分派。

使用函数的所有参数，而非只用第一个，来决定调用哪个方法被称为多重分派。多重分派对于数学代码来说特别有用，人工地将运算视为对于其中一个参数的属于程度比其他所有的参数都强的这个概念对于数学代码是几乎没有意义的：x + y 中的加法运算对 x 的属于程度比对 y 更强？一个数学运算符的实现普遍基于它所有的参数的类型。即使跳出数学运算，多重分派是对于结构和组织程序来说也是一个强大而方便的范式。


**优化方法的使用**

 - 只根据一个参数分派

Julia是多重分派的模式，那如果我们在定义方法的时候想只根据第一个参数分派怎么办？我们可以采用“名字级联”的方式，在内部做好分派。例如我们可以采用下面的方式：
```Julia
f(x::A, y) = _fA(x, y)
f(x::B, y) = _fB(x, y)
```
这样对于函数f()来说，只根据第一个参数就可以进行分派了。





