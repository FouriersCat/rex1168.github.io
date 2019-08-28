---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Python基本语法
description: "."
chapter: 1
category: python-basic
post-headings:
author-link: #
no-video: true
publish-date: 2019-08-25
thumbnail: /static/img/course_cover-small/python.png
index: 2
---



## Python3基本语法

### 行与缩进

&emsp;&emsp;Python中一个特色就是使用缩进来代表代码块，不需要使用大括号{}。缩进的空格数随意，但必须保证每段代码块的缩进是相同的，每行结尾的分号(;)可加可不加（一般都不加）。如下面代码所示。
```
if True:  
    a = 1  
    b = 1  
else:  
    a = 2; 
    b = 2;
```

很多同学刚开始写Python时会忽略这一点，比如下面这样，就会报错。
```
if True:  
    a = 1  
    b = 1  
else:  
a = 2  
b = 2
IndentationError: expected an indented block
```

### 多行语句

&emsp;&emsp;通常情况下一行就可以写完一条语句，如果语句很长，需要占用多行，可以使用反斜杠（\）来实现。
```
dict = {'a': 1, \  
        'b': 2, \  
        'c': 3}
```
### 条件控制
```
score = 88
if score < 60:
    print('failed')
elif score < 80:
    print('pass')
else:
    print('good')
```
&emsp;&emsp;与其他语言的if用法基本一致，也可以嵌套使用。Python中没有switch case的语法，官方解释如下。

<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/switch.png?raw=true" width = "780" height = "180" alt="" />

</center>

### 循环控制

 - while循环   
&emsp;&emsp;与其他语言的while用法基本一致，计算1到100的和示例如下。
```
n = 100
sum = 0
cnt = 1
while cnt <= n:
    sum += cnt 
    cnt += 1
print(sum)
```
 - for循环   

&emsp;&emsp;for循环可以遍历任何对象。
```
>>> lst = [1, 2, 3, 4]
>>> for i in lst:
        print(i)
1
2
3
4
```
&emsp;&emsp;遍历数字序列：
```
>>> for i in range(5):
        print(i)
0
1
2
3
4
```

### 迭代器与生成器
&emsp;&emsp;迭代是python中的一个很强大的功能，是访问集合元素的一种方式。迭代器是一个可以记住遍历的位置的对象。
```
>>> lst = [1, 2, 3, 4]
>>> it = iter(lst)
>>> print(next(it))
1
>>> print(next(it))
2
>>> it = iter(lst)
>>> for x in it:
        print(x)
1
2
3
4
```

&emsp;&emsp;生成器是一个返回迭代器的函数，只能用于迭代操作。在调用生成器运行的过程中，每次遇到yield时函数会暂停并保存当前所有的运行信息，返回yeild的值，并在下一次执行next()方法时从当前位置继续运行。
```
def fibonacci(n):  
    a, b, counter = 0, 1, 0  
    while True:  
        if(counter > n):  
            return  a
        yield a  
        a, b = b, a + b  
        counter += 1  
f = fibonacci(10) 
```
此处是a就是把前面所有的值都记录起来。

### 函数
 - 函数参数
```
def func():
    return
```
不带参数的return语句返回None。
 1. 不带参数的函数
```
def func():  
    print('Hello world!')  
```
 2. 带参数的函数
```
def func(str):  
    print(str)  
func('Hello World!')  
```

 3. 可变类型和不可变类型函数参数

&emsp;&emsp;当函数参数为不可变类型时（Number、String、Tuple和Sets），类似与C++中的传递形参，不会改变实参的值，即不会改变参数的值；当函数参数位可变类型（List和Dict）时，类似与C++中的引用传递，会改变传入参数的值。
```
def ChangeNum(a):  
    print('函数中参数传入时的值为：%d'% a)  
    a = 2  
    print('在函数中将参数值改变为：%d' % a)  
b = 1  
ChangeNum(b)  
print('函数运行后原参数的值为:%d'% b) 
```
输出结果为：
```
函数中参数传入时的值为：1  
在函数中将参数值改变为：2  
函数运行后原参数的值为: 1  
```
当输入的参数为List时，会有如下结果。
```
def ChangeList(lst):  
    lst.append([4,5])  
lst = [1, 2, 3]  
print('原始序列:',lst)  
ChangeList(lst)  
print('函数执行后的序列为：',lst)  
```
输出结果为：
```
原始序列: [1, 2, 3]  
函数执行后的序列为： [1, 2, 3, [4, 5]] 
```
 4. 关键字参数
```
def keyword_fnc(name, age):  
    print('name:', name)  
    print('age:', age)  
    return  
keyword_fnc('Jim', 28)  
keyword_fnc(age = 28, name = 'Jim')  
```
 5. 不定长参数
```
def func1(*arg):  
    print(arg)  
func1(1,2,3)  
```

则输出结果为：
```
(1, 2, 3)
```
&emsp;&emsp;当参数前加*后即说明是一个不定长参数，它是一个tuple；如果前面加了**，表示为关键字参数，即输入为一个dict。
```
def func2(*args, **kwargs):  
    print(args)  
    print(kwargs)  
func2(1, 2, 3, a = 4, b = 5)  
```
则输出结果为：
```
(1, 2, 3)  
{'a': 4, 'b': 5}  
```
 6. 默认参数
```
def keyword_fnc(name, age = 28):  
    print('name:', name)  
    print('age:', age)  
    return  
keyword_fnc('Jim', 26)  
keyword_fnc('Jim') 
```
输出结果为：
```
name: Jim  
age: 26  
name: Jim  
age: 28  
```
 - lambda函数   

&emsp;&emsp;lambda是一个表达式，比用def的方式定义函数形式简单，但跟C++中的内联函数不同，内联函数的目的是调用小函数时不占用栈内存而增加运行效率。
```
>>> sum = lambda arg1, arg2: arg1 + arg2
>>> print(sum(1,2))
3
```
 - 提高函数执行效率

 &emsp;&emsp;map、reduce和filter在大数据处理中经常使用的三个内建函数，都是为了减少for的使用，提高效率。Map函数有两个参数：函数和序列，即map将函数依次作用于序列的每个元素中，在python3中，map/reduce/filter的输出要手动转换成list。
```
>>> def func(x):
        return x*2
>>> list(map(func, [1,2,3]))
[2, 4, 6]
```
跟lambda函数配置使用更加简单：
```
>>> list(map(lambda x: x * 2, range(1,4)))
[2, 4, 6]
```
Map函数跟for语句的运行时间几乎相同，两种方式不一定哪一种会稍微快一些。
```
import time

N = 1000000
def func():
    res = []
    for x in range(1,N+1):
        res.append(x*x)
    return res
start1 = time.time()
a = func()
end1 = time.time()
start2 = time.time()
b = list(map(lambda x:x*x, range(1,N + 1)))
end2 = time.time()
     
print(end1 - start1)
print(end2 - start2)
```
输出结果：
```
0.2040114402770996
0.16900968551635742
```

&emsp;&emsp;reduce函数在pytyon3中被从全局namespace中移除了，被放置在functools模块中。Reduce函数可以按照给定的方法把输入参数的序列缩减为单个的值：首先从序列中去除头两个元素并把它传递到二元函数中，求出一个值，再把这个结果与后面一个元素放到二元函数中，直到最后一个值。


### 变量作用域

&emsp;&emsp;Python中作用域有4种：

 - L(Local)局部作用域
 - E(Enclosing)闭包函数外的函数中
 - G(Global)全局作用域
 - B(Built-in)内建作用域

以L->E->G->B的规则查找。
```
b_var = float(1.1)          # 内建作用域  
g_var = 1                   # 全局作用域  
def func1():  
    o_var = 2               # 闭包函数外的函数  
    def func2():  
        l_var = 3           # 局部作用域 
```
&emsp;&emsp;python中只有模块、类和函数才会引入新的作用域，其他的代码块（if/for/while）是不会引入作用域的，在这些语句块内定义的变量外部是可以访问的。
平时的使用中最容易出错的是全局变量和局部变量，当在函数外部定义了一个全局变量，在函数内部又重新定义了这个变量，那么这个变量只是函数内部的局部变量，不影响全局变量的值。
```
var = 1  
def func():  
    var = 2  
    print(var)  
func()  
print(var)  
>>> 1
```
&emsp;&emsp;global和nonlocal关键字，从字面意思也可以看出，是把变量按全局变量或非局域变量使用。
```
var = 1  
def func():  
    global var   
    var = 2  
    print(var)  
func()  
print(var)  
```
打印结果都是2














