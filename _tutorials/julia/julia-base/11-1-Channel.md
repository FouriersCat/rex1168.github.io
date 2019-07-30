---
youtube_id: Tv_4tP1NMbw
qq_video_id: v08338ls39l
b_av: 47775635
b_cid: 83684997
b_page: 27
title: Julia协程
description: "协程"
chapter: 11
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 27
---

 
## 协程

协程是一张轻量级的线程，通过Channel实现多个任务之间的通信。

Channel可以看作是一个管道，一端可读，一端可写。

 - 不同的task可以通过`put!`往同一个channel中并发地写入
 - 不同的task也可以通过`take!`从同一个 channel 并发地取数据

Channel的定义方式
```
# Channel(func::Function, ctype=Any, csize=0, taskref=nothing)
```

```
c1 = Channel(32)
c2 = Channel{Float64}(32)
put!(c1, 1)
put!(c2, 2)
take!(c2)
>>2.0
```
关闭Channel，关闭后就不能再写入
```
close(c1)
put!(c1, 3)
>>error
```
但可以读出,`fetch`只能读第一个数据，但不会把该数据从Channel中删除，但`take!`会读出后删除。

 - 如果一个 Channel 是空的，读取的 task(即执行 take! 的task)会被阻塞直到有新的数据准备好了。
 - 如果一个 Channel 是满的，那么写入的 task(即执行 put! 的 task)则会被阻塞，直到 Channel 有空余。
 - isready 可以用来检查一个 channel 中是否有已经准备好的元素，而等待一个元素准备好 则用 wait
 - 一个 Channel 一开始处于开启状态，也就是说可以被 take! 读取和 put! 写入。close 会关闭一个 Channel，对于一个已经关闭的 Channel，`put!` 会失败

Channel 可以在 for 循环中遍历，此时，循环会一直运行直到 Channel 中有数据，遍历过程中会取遍加入到 Channel 中的所有值。一旦 Channel关闭或者取空了，for 循环就会终止。

```
c = Channel{Int}(10)
foreach(i->put!(c, i), 1:3) 
```
此时如果我们直接用`for`去遍历`c`中的内容，则在读取完后会一直等待Channel中有新的数据进来，如果在REPL上运行，则会停在那里。
```
data = [i for i in c]
```
如果我们先把Channel关闭，再用`for`去遍历，则会读出Channel中的内容，因此此时`c`已经关闭，`for`不会再等待写入了。

```
close(c); 
data = [i for i in c]
```

用Channel完成多任务之间的数据交互

新建两个Channel，一个是Int型，一个是Tuple型，对于每个任务，延时一段时间后，开始执行该任务的内容，并将结果放入到results中。
```
const jobs = Channel{Int}(32)
const results = Channel{Tuple}(32)
function do_work()
   for job_id in jobs
       exec_time = rand()
       sleep(exec_time)                # simulates elapsed time doing actual work
                                       # typically performed externally.
       put!(results, (job_id, exec_time))
   end
end
```

向jobs中放入数据
```
function make_jobs(n)
   for i in 1:n
       put!(jobs, i)
   end
end
```
写入12个数据
```
n = 12
@async make_jobs(n)
```
@async表示把后面的表达式放到Task里，并加入到程序的执行列表中

开四个任务来处理
```
for i in 1:4 # start 4 tasks to process requests in parallel
   @async do_work()
end
```

取出4个任务的执行结果
```
@elapsed while n > 0 # print out results
   job_id, exec_time = take!(results)
   println("$job_id finished in $(round(exec_time; digits=2)) seconds")
   global n = n - 1
end
>>1 finished in 0.51 seconds
 4 finished in 0.62 seconds
 3 finished in 0.75 seconds
 2 finished in 0.81 seconds
 5 finished in 0.55 seconds
 8 finished in 0.35 seconds
 6 finished in 0.77 seconds
 9 finished in 0.38 seconds
 12 finished in 0.11 seconds
 7 finished in 0.88 seconds
 11 finished in 0.25 seconds
 10 finished in 0.75 seconds
```




</font>


