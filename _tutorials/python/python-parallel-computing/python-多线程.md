---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Python多线程
description: "."
chapter: 1
category: python-parallel-computer
post-headings:
author-link: #
no-video: true
publish-date: 2019-08-26
thumbnail: /static/img/course_cover-small/python.png
index: 1
---



<font size=4>


### 多线程和多进程的区别

&emsp;&emsp;我们首先要把这个问题搞清楚，这也是很多公司面试时的一个常见问题。    
&emsp;&emsp;在编程中我们所说的多线程跟CPU的多线程并不是一个意思，比如i7-7700K,是4核8线程的一个CPU，但并不是说我们程序中最多只能创建8个线程。我们可以创建几百个线程，这跟内存大小和每个线程的栈大小都有关系。进程我们可以简单的理解为应用程序，每个应用程序都会至少占用一个进程，在进程中我们可以选择使用单线程或者多线程的方式。

&emsp;&emsp;关于多进程和多线程，教科书上最经典的一句话是“进程是资源分配的最小单位，线程是CPU调度的最小单位”。这句话很直观，也很容易理解，但在具体的使用中如果选择不好会导致很大的麻烦。

&emsp;&emsp;首先来直观的对比多线程和多进程：[参考csdn](https://blog.csdn.net/yu876876/article/details/82810178)


对比维度  | 多进程 | 多线程 
------- | ------ |------
数据共享、同步 | 数据共享复杂，需要用IPC；数据是分开的，同步简单 | 共享进程数据，这也导致了数据同步复杂
内存/CPU| 占用内存多，CPU利用率低 | 占用内存少，CPU利用率高
创建、销毁、切换 | 复杂、速度慢    | 简单、速度快
调试 | 简单 | 复杂
可靠性 | 进程间不会相互影响 | 一个线程挂掉导致整个进程挂掉
分布式 | 适用于多核多机分布式，扩展到多台机器简单 | 适用于多核分布式


1. 需要频繁创建销毁的优先用线程

&emsp;&emsp;这种原则最常见的应用就是Web服务器了，来一个连接建立一个线程，断了就销毁线程，要是用进程，创建和销毁的代价是很难承受的

2. 需要进行大量计算的优先使用线程

&emsp;&emsp;所谓大量计算，当然就是要耗费很多CPU，切换频繁了，这种情况下线程是最合适的。

这种原则最常见的是图像处理、算法处理。

3. 强相关的处理用线程，弱相关的处理用进程

&emsp;&emsp;什么叫强相关、弱相关？理论上很难定义，给个简单的例子就明白了.一般的Server需要完成如下任务：消息收发、消息处理。“消息收发”和“消息处理”就是弱相关的任务，而“消息处理”里面可能又分为“消息解码”、“业务处理”，这两个任务相对来说相关性就要强多了。因此“消息收发”和“消息处理”可以分进程设计，“消息解码”、“业务处理”可以分线程设计。当然这种划分方式不是一成不变的，也可以根据实际情况进行调整。

5. 都满足需求的情况下，用你最熟悉、最拿手的方式

&emsp;&emsp;至于“数据共享、同步”、“编程、调试”、“可靠性”这几个维度的所谓的“复杂、简单”应该怎么取舍，我只能说：没有明确的选择方法。但我可以告诉你一个选择原则：如果多进程和多线程都能够满足要求，那么选择你最熟悉、最拿手的那个。



### 多线程的基本应用

&emsp;&emsp;python的标准库中提供了两个线程模块：_thread和threading，其中_thread是低级模块，threading是高级模块，对_thread进行了封装，绝大多数情况下，我们只需使用threading这个高级模块即可。    

 1. 添加线程
```
>>> import threading
>>> threading.active_count() #查看当前已激活的线程数
1
>>> threading.enumerate() #查看线程的信息
[<_MainThread(MainThread, started 21340)>] #当前已激活的线程只有主线程
```
 2. 创建一个新的线程
```
import threading
import time 

def T1():
    print('T1 started')
    print(threading.current_thread()) #查看当前正在运行的线程
    time.sleep(1)
    print('T1 finished')

def T2():
    print('T2 started')
    print(threading.current_thread()) #查看当前正在运行的线程
    print('T2 finished')
    
def main():
    add_T1 = threading.Thread(target = T1, name = 'T1') #创建线程T1
    add_T2 = threading.Thread(target = T2, name = 'T2') #创建线程T2
    add_T1.start() #线程T1启动
    print(threading.active_count()) #查看当前激活的线程
    print(threading.enumerate()) #查看线程信息
    print(threading.current_thread()) #查看当前正在运行的线程
    add_T1.join() #等待T1运行完成后再运行后面的程序
    add_T2.start() #线程T2启动
    print('T1 and T2 finished')
    print(threading.active_count()) #查看当前激活的线程
    print(threading.enumerate()) #查看线程信息
    print(threading.current_thread()) #查看当前正在运行的线程
```
上面代码打印的结果为：
```
33. T1 started
34. 2
35. <Thread(T1, started 16188)>
36. [<_MainThread(MainThread, started 9836)>, <Thread(T1, started 16188)>]
37. <_MainThread(MainThread, started 9836)>
38. T1 finished
39. T2 started
40. T1 and T2 finished
41. <Thread(T2, started 18548)> #因为T2没有join，所以该行打印结果可能会在上面一行的后面
42. T2 finished
43. 1
44. [<_MainThread(MainThread, started 9836)>]
45. <_MainThread(MainThread, started 9836)>
```
 3. 线程函数输出

&emsp;&emsp;如果线程函数T1()和T2()有返回值，需要用到queue来实现，而不能使用return。
```
import threading
import time
from queue import Queue
    
def thread_func(a,q):
    b = a*2
    q.put(b)

def main():
    q = Queue() #不能在线程函数中定义
    threads = []
    lst = [1,10,20,30]
    for i in range(4):
        t = threading.Thread(target = thread_func, args = [lst[i], q])
        t.start()
        threads.append(t)
    [t.join() for t in threads]
    res = []
    for i in range(4):
        res.append(q.get())
    print(res)
    
if __name__ == '__main__':
    main()
```
### 多线程GIL
&emsp;&emsp;首先我们来看下面一段代码，用4个线程分别计算一个序列（n个点）的和，即总共计算了4n个点，再用python中非多线程的方式计算这4n个点的和。
```
import threading
import time
from queue import Queue

def normal_func(lst):
    return sum(lst)

def thread_func(lst,q):
    res = sum(lst)
    q.put(res)
    
def mulThreads(lst):
    q = Queue() #不能在线程函数中定义
    threads = []
    for i in range(4):
        t = threading.Thread(target = thread_func, args = [lst, q])
        t.start()
        threads.append(t)
    [t.join() for t in threads]
    res = 0
    for i in range(4):
        res += q.get()
    return res 
    
def main():
    N = 10000000
    lst = list(range(N))
    t0 = time.time()
    res1 = normal_func(lst*4)
    print('非多线程处理时间：',time.time() - t0)
    print('非多线程处理时间：',res1)
    t1 = time.time()
    res2 = mulThreads(lst)
    print('多线程处理时间:',time.time() - t1)
    print('多线程处理时间：',res2)

if __name__ == '__main__':
    main()
```
运行结果为：
```
非多线程处理时间： 1.3539998531341553
非多线程处理时间： 199999980000000
多线程处理时间:   1.0069997310638428
多线程处理时间：  199999980000000
```
&emsp;&emsp;从计算结果可以看出，用4线程的方式并未比普通的非多线程的方式快4倍，甚至连2倍都没有，这是为什么呢？    
&emsp;&emsp;原因就在于GIL，即全局解释器锁，GIL让python在多线程时还是一次只能处理一个东西，换言之，python的多线程不是真正意义上的多线程，而其实只有一个线程，只是这个线程在python中定义的多线程之间来回切换，造成多线程的假象。既然是假的多线程，那还有什么意义？     
&emsp;&emsp;一般来讲，我们的程序可分为IO密集型和CPU密集型，IO密集型是指程序中有什么数据传输操作，数据传输速度一般都是小于CPU主频的，因此对于这种程序来说，CPU觉得多数时间处于等待IO的状态（pyhon经常被用于网络爬虫，而爬虫就是典型的IO密集型）；而CPU密集型是指程序中计算较多，CPU的绝大多数时间都用于计算了，比如做卷积或者FFT等操作。    
&emsp;&emsp;如果是IO密集型，我们的多线程就可以提现很好的性能，在CPU等待当前线程的数据时去处理其他线程的任务；而如果是CPU密集型，python的多线程则没有软用了。反过来讲，对于CPU密集型的程序，我们就应该用其他更适合的语言进行处理。
下面的例子是在上面的程序基础上，加上延时，即假设每两个数数据之间相隔了0.00001s，再来看处理结果。
```
import threading
import time
from queue import Queue
    
def normal_func(lst):
    time.sleep(len(lst)*0.00001)
    return sum(lst)
    
def thread_func(lst,q):
    time.sleep(len(lst)*0.00001)
    res = sum(lst)
    q.put(res)
    
def mulThreads(lst):
    q = Queue() #不能在线程函数中定义
    threads = []
    for i in range(4):
        t = threading.Thread(target = thread_func, args = [lst, q])
        t.start()
        threads.append(t)
    [t.join() for t in threads]
    res = 0
    for i in range(4):
        res += q.get()
    return res 

def main():
    N = 100000
    lst = list(range(N))
    t0 = time.time()
    res1 = normal_func(lst*4)
    print('非多线程处理时间：',time.time() - t0)
    print('非多线程处理时间：',res1)
    t1 = time.time()
    res2 = mulThreads(lst)
    print('多线程处理时间:',time.time() - t1)
    print('多线程处理时间：',res2)
    
if __name__ ==
    main()
```
运行结果为：
```
非多线程处理时间： 4.034999847412109
非多线程处理时间： 19999800000
多线程处理时间:   1.0199999809265137
多线程处理时间：  19999800000
```
&emsp;&emsp;可以看出，加上IO的等待后，多线程的优势就体现了出来。

### 多线程同步
&emsp;&emsp;当两个线程中都需要对同一个变量进行操作时，就会涉及线程间同步的问题，如下面的例子。
```
import time, threading

a = 10

def thread_func1():
    global a 
    for i in range(10):
        a = a + 1
        time.sleep(0.1)
        print(a)
    
def thread_func2():
    global a 
    for i in range(10):
        a = a + 10
        time.sleep(0.1)
        print(a)
    
def mulThreads():
    global a
    t1 = threading.Thread(target = thread_func1)
    t2 = threading.Thread(target = thread_func2)
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    
if __name__ == '__main__':
    mulThreads()
```
运行结果为：
```
21
22
32
33
43
44
54
55
65
66
76
77
87
88
98
99
109
110
120
120
```

&emsp;&emsp;可以看出，在执行过程中，会在线程之间切换，就会出现有时加1有时加10的情况，如果想等1都加完之后再加10，可采用lock的方式。
```
import time, threading

a = 10
lock = threading.Lock()

def thread_func1():
    global a,lock
    lock.acquire()
    for i in range(10):
        a = a + 1
        time.sleep(0.1)
        print(a)
    lock.release()
    
def thread_func2():
    global a 
    lock.acquire()
    for i in range(10):
        a = a + 10
        time.sleep(0.1)
        print(a)
    lock.release()

def mulThreads():
    global a
    t1 = threading.Thread(target = thread_func1)
    t2 = threading.Thread(target = thread_func2)
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    
if __name__ == '__main__':
    mulThreads()
```

运行结果为：
```
11
12
13
14
15
16
17
18
19
20
30
40
50
60
70
80
90
100
110
120
```
&emsp;&emsp;在lock.acquire()后面的变量都会被锁住，直到lock.release()后才能被其他线程操作。



</font>




































