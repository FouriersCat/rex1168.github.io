---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Python多进程
description: "."
chapter: 1
category: python-parallel-computing
post-headings:
author-link: #
no-video: true
publish-date: 2019-08-26
thumbnail: /static/img/course_cover-small/python-parallel.png
index: 2
---




&emsp;&emsp;

python中的多线程并不是真正意思上的多线程，如果想充分利用多核CPU的资源，要使用多进程来完成。多进程的使用方式跟多线程非常相似。

### 1. 创建新的进程
```
import multiprocessing
import time 
    
def mp_func(var):
    time.sleep(0.1)
    print(var)
    
def multi_pros():
    p = multiprocessing.Process(target = mp_func, args = (3,))
    p.start()
    p.join()
    print('p.pid:',p.pid)
    print('p.name:',p.name)
    print('p.is_alive:',p.is_alive())
    
if __name__ == '__main__':
    multi_pros()
```
### 2. 进程函数间通信
&emsp;&emsp;跟线程一样，可以用Queue，也可以用Pipes，这里我们以Queue介绍用法：
```
import multiprocessing 
import time
from queue import Queue

def mp_func(a,q):
    b = a*2
    q.put(b)

def main():
    q = multiprocessing.Queue() #要用mp的Queue
    processes = []
    lst = [1,10,20,30]
    for i in range(4):
        p = multiprocessing.Process(target = mp_func, args = (lst[i], q,))
        p.start()
        processes.append(p)
    [p.join() for p in processes]
    res = []
    for i in range(4):
        res.append(q.get())
    print(res)

if __name__ == '__main__':
    main()
```
### 3. 多进程同步
&emsp;&emsp;在多线程的3.4节中，我们使用Lock来使多线程同步去，同样，我们将3.4节中的例子改成多进程的方式，看处理结果。
```
import time, multiprocessing

a = 10
lock = multiprocessing.Lock()

def mp_func1():
    global a,lock
    lock.acquire()
    for i in range(10):
        a = a + 1
        time.sleep(0.1)
        print(a)
    lock.release()

def mp_func2():
    global a 
    lock.acquire()
    for i in range(10):
        a = a + 10
        time.sleep(0.1)
        print(a)
    lock.release()

def mulProcess():
    global a
    p1 = multiprocessing.Process(target = mp_func1)
    p2 = multiprocessing.Process(target = mp_func2)
    p1.start()
    p2.start()
    p1.join()
    p2.join()
    
if __name__ == '__main__':
    mulProcess()
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
```
&emsp;&emsp;输出结果与多线程不同，原因就是在多线程中，变量a是几个线程共享的，a被一个线程修改后，另一个线程再取到a时是已经修改过的值。所以对于多线程，在func1()累加完后，a变为20，再被func2()累加时，a会从20开始。但在多进程中，每个进程都会复制一个a，这样线程之间a的值就是不同的，所以在func1()累加完后，a变为20，而func2()再操作时，a是从10开始计算的，因此处理的结果与多线程不同。
那怎么让多进程之间的变量也可以共享呢？
&emsp;&emsp;multiprocessing中的Lock可以实现这一功能，我们定义一个mp.Lock()变量，并将该变量作为参数传到进程函数中，即可实现变量的共享了。
```
import time, multiprocessing
    
def mp_func1(a, lock):
    lock.acquire()
    for i in range(10):
        a.value = a.value + 1
        time.sleep(0.5)
        print(a.value)
    lock.release()
    
def mp_func2(a, lock):
    lock.acquire()
    for i in range(10):
        a.value = a.value + 10
        time.sleep(0.5)
        print(a.value)
    lock.release()
    
def mulProcess():
    a = multiprocessing.Value('i', 10)
    lock = multiprocessing.Lock()
    p1 = multiprocessing.Process(target = mp_func1, args = (a, lock, ))
    p2 = multiprocessing.Process(target = mp_func2, args = (a, lock, ))
    p1.start()
    p2.start()
    p1.join()
    p2.join()
    
if __name__ == '__main__':
    mulProcess()
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

### 4. 进程池
&emsp;&emsp;当需要启动大量的子进程时，可以用进程池的方式批量创建子进程。
```
import multiprocessing as mp 
import time
    
def mp_func(x):
    print('input:',x)
    time.sleep(0.5)
    

def multipro():
    pool = mp.Pool(3)
    for i in range(4):
        pool.apply_async(mp_func, (i,))
    print('Waiting for print...')
    pool.close()
    pool.join()
    
if __name__ == '__main__':
    multipro()
```
运行结果为：
```
Waiting for print...
input: 1
input: 2
input: 0
input: 3
```

&emsp;&emsp;Pool中如果未指定进程数，默认是CPU的核心数；由于我们只创建了3个进程，而有4个进程需要处理，所以第3个进程要等前面三个处理完成后才会执行，因此前3个是并行执行，第4个最后执行。





