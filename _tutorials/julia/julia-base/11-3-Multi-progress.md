---
youtube_id: peHsa2kD6-E
qq_video_id: t0833rht5ar
b_av: 61523729
b_cid: 106600483
b_page: 6
title: Julia多进程
description: "多进程"
chapter: 11
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 29
---



## 多进程


多进程也叫多核心或者分布式处理，就是用一个CPU的多个核心或者多个CPU进行编程。

Julia 中的分布式编程基于两个基本概念：远程引用(remote references)和远程调用(remote calls)。远程引用是一个对象，任意一个进程可以通过它访问存储在某个特定进程上的对象。远程调用指是某个进程发起的执行函数的请求，该函数会在另一个（也可能是同一个）进程中执行。


远程引用有两种类型：Future 和 RemoteChannel。

一次远程调用会返回一个 Future 作为结果。
远程调用会立即返回；也就是说，执行远程调用的进程接下来会继续执行下一个操作，而远程调用则会在另外的进程中进行。你可以通过对返回的 Future 执行 wait 操作来等待远程调用结束，然后用 fetch 获取结果。

对于 RemoteChannel 而言，它可以被反复写入。
例如，多个进程可以通过引用同一个远程 Channel 来协调相互之间的操作。

每个进程都有一个对应的 id，提供 Julia 交互环境的进程的 id 永远是1。我们把用来执行并行任务的进程称为 “worker”，假如总共只有一个进程，那么进程1就被认为是 worker，否则，除了进程1以外的进程都称作 worker。

REPL上多进程使用方式，在julia的可执行文件的路径下打开命令行，执行`julia -p n`，就是启动n个进程的julia
```
# 指定进程2来生成一个3x4的随机矩阵
r = remotecall(rand, 2, 3, 4)
# 在进程2中计算
s = @spawnat 2 1 .+ fetch(r)  # fetch是把r中的数据获取到当前进程中
# 取得s
fetch(s)
```

也可以让Julia自行指定进程
```
r = @spawn rand(2,2)
s = @spawn 1 .+ fetch(r)
fetch(s)
```

要想让代码并行执行，需要对所有进程都可见
```
function f1(a,b)
    a + b
end
```

```
fetch(@spawn f1(2,3))
```
这是因为f1()函数只对进程1可见，别的进程不可见。f1()在进程1中定义的，因为其他进程并不可见，但rand是在Base中，其他进程都可见

为了让f1在所有进程中都可见，可以使用@everywhere宏来定义f1

```
@everywhere f2(a,b) = a + b
fetch(@spawn f2(2,3))
```




