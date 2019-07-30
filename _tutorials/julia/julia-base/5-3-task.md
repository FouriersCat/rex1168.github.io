---
youtube_id: 1vDfBfak7WA
qq_video_id: y0833d2qbwh
b_av: 47775635
b_cid: 83682886
b_page: 16
title: Julia任务
description: "Julia中的协程介绍"
chapter: 5
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 16
---





**任务（也称为协程）**

被称为为对称协程、轻量级线程、协同多任务等。(与线程有区别，在后面的并行计算中会讲)

如果一个计算以任务的方式执行，那它就很可能会被其他任务中断，原先的任务在恢复后，会从被中断的地方继续工作，这种过程看似很像函数调用，但有两点不同：
 1. 任务切换不需要任何空间，因此可以完成任意数量任务的切换，而且无需考虑堆栈问题。
 2. 任务切换可以按照任何顺序来进行。

任务比较适合生产者-消费者模式，一个过程用来生产值，另一个用来消费值。消费者不能简单的调用生产者来得到值，因为两者的执行时间不一定协同。在任务中，两者则可以正常运行。

Julia中提供了Channel来解决生产者消费者的协同问题，其实Channel就是一个FIFO（first-in first-out）队列。使用`put!`和`take!`函数来具体实现。
```Julia
function producer(c::Channel)
           put!(c, "start")
           for n=1:4
               put!(c, 2n)
           end
           put!(c, "stop")
end
```
然后我们新建一个Channel组件，来消费数值。
```
chnl = Channel(producer)
take!(chnl)
>>"start"
take!(chnl)
>>2
take!(chnl)
>>4
take!(chnl)
>>6
take!(chnl)
>>8
take!(chnl)
>>"stop"
```
也可以使用`for`循环的方式
```Julia
for x in Channel(producer)
    println(x)
end
>>start
  2
  4
  6
  8
  sotp
```
在两次调用`put!()`之间，生产者的执行是挂起的，此时由消费者接管控制。

任务的一个特性就是随着任务的结束，channel对象会自动关闭，无需人为干预。

把一般的函数放到task中：

```Julia
function mytask(n)
    for i=1:n
        println(i)
    end
end

taskHdl = @task mytask(10)
println(istaskdone(taskHdl))
schedule(taskHdl)
println(current_task())
println(istaskdone(taskHdl))
>>false
1
2
3
4
5
6
7
8
9
10
Task (runnable) @0x000000000d41eb30
true
```





