---
youtube_id: -5WEslpX_WU
qq_video_id: x08330glmhc
b_av: 61523729
b_cid: 106583672
b_page: 5
title: Julia多线程
description: "多线程"
chapter: 11
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 28
---

## 多线程

要用到Threads模块，Threads是Base中的一个模块，程序中默认是`using Base`的，因此Threads可以直接使用

在REPL上查看当前的线程数，默认是启动一个线程

使用`export JULIA_NUM_THREADS=4`(Linux OSX)或`set JULIA_NUM_THREADS=4`(Windows)来设置启动4个线程

当我们要在Jupyter中使用多个线程时，可以在Julia的运行目录中下打开命令行，先设置线程数，再启动Julia，在windows下的操作如下：

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97.png)

这样就可以在Jupyter中使用4个线程进程操作了。

每个线程都有一个id，可以用`Threads.threadid()`查看线程的id
```
Threads.threadid()
>>1
```
我们查看10次线程的id
```
a = zeros(10)
Threads.@threads for i = 1:10
   a[i] = Threads.threadid()
end
>>1
  1
  1
  2
  2
  2
  3
  3
  3
  4
  4
```
由于我们只启动了4个线程，因此线程id也只到4，虽然我们进行了10次线程的调用。

**原子操作**

所谓原子操作是指不会被线程调度机制打断的操作；这种操作一旦开始，就一直运行到结束，中间不会有任何context switch（切换到另一个线程）。

我们先看一个使用多线程但不使用原子操作的例子。
```
using Base.Threads  # 直接使用Threads中的函数
nthreads()          # 查看当前线程数
acc = Ref(0)
@threads for i in 1:1000   # 用多线程进行计数
    acc[] +=1
end
println(acc[])
>>307
```
这是因为线程1对acc操作时，先取到它的数据为x，此时很可能线程2也在取它的值，取到的也是x，所以两个线程分别对其加1，结果是x+1，而不是x+2.这就是线程之间没有做同步机制。

如果使用了原子操作，则当前线程在对acc进行操作时，别的线程是不能操作的，只能等acc+1之后再对它进行操作。
```
acc = Atomic{Int64}(0)
@threads for i in 1:1000
  atomic_add!(acc, 1)
end
println(acc[])
>>1000
```

