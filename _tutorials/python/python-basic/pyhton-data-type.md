---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Python基本数据类型
description: "."
chapter: 1
category: python-basic
post-headings:
author-link: #
no-video: true
publish-date: 2019-08-26
thumbnail: /static/img/course_cover-small/python.png
index: 1
---



&emsp;&emsp;Python3中有6中标准的数据类型：
 - Number（数字）
 - String（字符串）
 - List（列表）
 - Tuple（元组）
 - Set（集合）
 - Dictionary（字典）    

其中Number、String、Tuple和Sets是不可变类型，List和Dictionary是可变类型（后面讲解）。

### 1. Number（数字）    
&emsp;&emsp;包括int,float,bool,complex四种
```
>>> a,b,c,d,e = 10, 1.2, True, 3 + 4j, complex(5,6)  
>>> print(type(a), type(b), type(c), type(d),type(e))
<class 'int'> <class 'float'> <class 'bool'> <class 'complex'> <class 'complex'>
```

&emsp;&emsp;关于int相除的几点说明：   
 1. /是精确除法，两个int型相除的结果位float型
 2. //是向下整除法，%是求模
 3. round四舍五入取整，int向0取整，math.floor向下取整，math.ceil向上取整

```
>>> a = 10  
>>> b = 4  
>>> c = a/b  
>>> print(c)  
>>> print(type(a),type(b),type(c)) 
<class 'int'> <class 'int'> <class 'float'>
```

 4. Number属于不可变类型，对不可变类型的变量重新赋值，实际上是重新创建一个不可变类型的对象，并将原来的变量重新指向新创建的对象（如果没有其他变量引用原有对象的话（即引用计数为0），原有对象就会被回收）。若a=1，当a的值发生变化后，则a的内存地址发生了变化，用id()可查看变量的内存地址。

```
>>> a = 1
>>> id(a)
1503521232
>>> a = 2
>>> id(a)
1503521264
```

而如果令a=1,再令b=1，我们看a和b的内存地址：

```
>>> a = 1
>>> b = 1
>>> id(a)
1503521232
>>> id(b)
1503521232
```

&emsp;&emsp;可以看出，a和b的地址是一样的，对于不可变类型int，无论创建多少个不可变类型，只要值相同，都指向同个内存地址。   
&emsp;&emsp;但如果是两个大小相同的浮点数，发现它们并不是指向同个内存地址，这点和int类型不同（这与Python内存管理机制有关，Python对int类型和较短的字符串进行了缓存，无论声明多少个值相同的变量，实际上都指向同个内存地址）。

```
>>> a = 1.5
>>> b = 1.5
>>> id(a)
30347720
>>> id(b)
30347696
>>> a += 1
>>> a
2.5
>>> id(a)
30347576
```

 5. int是没有大小限制的,可使用bit_length()函数查看变量长度

```
>>> a = 10
>>> a.bit_length()
4
>>> b = 10**100
>>> b
10000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
>>> b.bit_length()
333
>>> type(b)
<class 'int'>
```

 6. Python中小数是用二进制表示，因此可能会出现很小的误差.
```
>>> 0.35+0.3
0.6499999999999999
```

 7. 可以用分数形式表示小数
```
>>> c = 0.5
>>> c.as_integer_ratio()
(1, 2)
>>> c = 0.34
>>> c.as_integer_ratio()
(6124895493223875, 18014398509481984)
```

 8. 任意精度对象的表示方法—通过context对象实现精度的调整
```
>>> import decimal  
>>> decimal.getcontext().prec = 50  
>>> a = decimal.Decimal(1)/decimal.Decimal(11)  
>>> a  
Decimal('0.090909090909090909090909090909090909090909090909091') 
```

### 2. String（字符串）

 - 字符串用单引号’ ’或双引号” ”表示（python中’ ’和” ”的效果是一样的）
 - 使用反斜杠（\）表示转义字符，使用r可以让反斜杠不发生转义
 - 索引从0开始，-1表示末尾
 - 加号（+）表示连接两个字符串
 - 星号（*）表示复制当前字符串，后面的数字表示复制的次数

```
>>> stir = 'abcdefg'            #输出结果  
>>> print(str)                  #abcdefg  
>>> print(str[0:1])             #a  
>>> print(str[-3:-1])           #ef  
>>> print(str[4:])              #efg  
>>> print(str * 2)              #abcdefgabcdefg  
>>> print(str + 'hijklmn')      #abcdefghijklmn  
```

 - 字符串也属于不可变类型，对str重新复制后，内存地址发生变化

```
>>> a = 'abc'
>>> id(a)
31608648
>>> a = 'cdb'
>>> id(a)
37263656
```

 - 字符串不能通过索引的方式改变
```
>>> a[1]
'd'
>>> a[1] = 'f'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
```

 - 正则表达式
```
import re
series = """
'02/18/2017 13:00:00',100,'list';
'02/28/2017 13:00:00',100,'list';
'02/08/2017 13:00:00',100,'list';
"""
dt = re.compile("'[0-9/:\s]+'")
result = dt.findall(series)
print(result)
["'02/18/2017 13:00:00'", "'02/28/2017 13:00:00'", "'02/08/2017 13:00:00'"]
```

 - 其他属性   

&emsp;&emsp;字符串对象有很多成员函数和成员变量，提供了很多常用功能，如转成首字母大写。

<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/string_else.png?raw=true" width = "280" height = "200" alt="" />

</center>
 

```
>>> str = 'this course is very useful'  
>>> str.capitalize()  
'This course is very useful'
```

### 3. List

 -  用方括号[]表示，里面的元素用逗号(,)分隔
 -  List也可以被所索引、截取
 -  索引从0开始，-1表示末尾
 -  加号（+）表示连接两列表
 -  星号（*）表示复制当前列表，后面的数字表示复制的次数
 -  List中元素内容可以更改
```
>>> a = [10, 20, [1,2,3]]
>>> a[0]
10
>>> a[0] = 15
>>> a
[15, 20, [1,2,3]]
```
 - List属于可变类型

&emsp;&emsp;当list的内容发生变化时，还是指向同个内存地址，因为list是可变类型，可以在原处修改。
```
>>> a = [1, 2, 3]
>>> id(a)
39059528
a.append(4)
>>> id(a)
39059528
```

&emsp;&emsp;当存在多个值相同的list变量时，他们分别指向不同的内存地址。
```
>>> a = [1, 2, 3]
>>> b = [1, 2, 3]
>>> id(a)
39060744
>>> id(b)
39059528
```

&emsp;&emsp;当把列表a直接赋给列表b时，他们指向同样的内存地址，因此当a改变时，b也随之改变。
```
>>> a = [1, 2, 3]
>>> b = a 
>>> a.append(4)
>>> b 
[1, 2, 3, 4]
>>> id(a)
39060936
>>> id(b)
39060936
```

 - 深拷贝

&emsp;&emsp;对于list来说，直接赋值时，aa直接指向了a的内存地址，因此当a的内容变化时，aa的内容也变化。
```
>>> a = [1, 2, 3]
>>> aa = [a, a]
>>> aa
[[1, 2, 3], [1, 2, 3]]
>>> a.append(4)
>>> aa
[[1, 2, 3, 4], [1, 2, 3, 4]]
```

&emsp;&emsp;用深拷贝的方式可以把a的值拷贝给aa，这样aa就不会受a的影响
```
>>> from copy import deepcopy
>>> a = [1, 2, 3]
>>> aa = [deepcopy(a)] * 2
>>> a.append(4)
>>> aa 
[[1, 2, 3], [1, 2, 3]]
```

 - List其他属性
```
>>> t1 = [1, 2, 3, 4]
>>> t1.append([5, 6])
>>> t1
[1, 2, 3, 4, [5, 6]]
>>> t1.extend([7, 8])
>>> t1
[1, 2, 3, 4, [5, 6], 7, 8]
>>> t1.insert(1, 'insert')
>>> t1
[1, 'insert', 2, 3, 4, [5, 6], 7, 8]
>>> t1.remove('insert')
>>> t1
[1, 2, 3, 4, [5, 6], 7, 8]
```

### 4. Tuple（元组）

&emsp;&emsp;元组与列表类似，不同之处在于元组的元素不能修改，用小括号()表示。

```
>>> a = (10, 'abc', [1,2,3], {'a1':10, 'a2':20})
>>> a[0]
10
>>> a[0] = 20
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```
&emsp;&emsp;创建只有1个元素的元组时要在元素后添加逗号
```
>>> t = (1)
>>> t
1
>>> type(t)
<class 'int'>
>>> t = (1,)
>>> t
(1,)
>>> type(t)
<class 'tuple'>
```

### 5. Set（集合）

 - Set是一个无序不重复元素的序列
 - 基本功能是成员关系测试和删除重复元素
 - 可以使用大括号{}或set()函数创建
 - 创建空集合必须用set()而不能用{}
 - 集合运算
```
>>> set1 = set('abcdefg')
>>> set2 = set('adfh')
>>> print(set1 - set2)
{'e', 'g', 'b', 'c'}
>>> print(set1 | set2)
{'f', 'c', 'a', 'g', 'b', 'e', 'h', 'd'}
>>> print(set1 & set2)
{'f', 'd', 'a'}
>>> print(set1 ^ set2)
{'c', 'g', 'b', 'e', 'h'}
```

 - Set对象的常用成员函数
```
>>> set1 = set(['a', 'ab', 'ba', 'abc'])
>>> set2 = set(['ab', 'a', 'abcd'])
>>> set1.union(set2)
{'abcd', 'abc', 'a', 'ba', 'ab'}
>>> set1.intersection(set2)
{'a', 'ab'}
>>> set1.difference(set2)
{'ba', 'abc'}
>>> set1.symmetric_difference(set2)
{'ba', 'abcd', 'abc'}
```

### 5. Dictionary（字典）
 - 灵活的可变容器模型，可存储任意类型对象
```
>>> dict1 = {'str1':'abcd', 'str2':'hijk'}
>>> dict1['str2']
'hijk'
```
 - 添加元素
```
>>> dict1['str3'] = 'opq'
>>> dict1
{'str1': 'abcd', 'str2': 'hijk', 'str3': 'opq'}
```
 - 修改元素
```
>>> dict1['str1'] = 'abcdefg'
>>> dict1
{'str1': 'abcdefg', 'str2': 'hijk', 'str3': 'opq'}
```
 - 删除元素
```
>>> del dict1['str1']
>>> dict1
{'str2': 'hijk', 'str3': 'opq'}
```

 - 清空字典、删除字典
```
>>> dict1.clear()
>>> dict1
{}
>>> del dict1
>>> dict1
Traceback (most recent call last):
 File "<stdin>", line 1, in <module>
NameError: name 'dict1' is not defined
```
 - 键是唯一的，不可变的，值不唯一，可变；因此键不能用list类型
```
>>> dict1 = {'Chinese': 90, 'Math': 99, 'English': 85, 'Chinese': 95}
>>> dict1
{'Chinese': 95, 'Math': 99, 'English': 85}
>>> dict2 = {['Chinese']: 90, 'Math': 99, 'English': 90}
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```













