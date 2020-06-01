---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: C++ 内联函数 inline的详细分析
description: ""
chapter: 1
category: cpp-base
post-headings:
author-link: #
no-video: true
publish-date: 2019-05-30
thumbnail: /static/img/course_cover-small/cpp.png
index: 4
---





## 概述

&emsp;&emsp;C++中map和unordered_map提供的是一种键值对容器，在实际开发中会经常用到，它跟Python的字典很类似，所有的数据都是成对出现的,每一对中的第一个值称之为关键字(key)，每个关键字只能在map中出现一次；第二个称之为该关键字的对应值(value)。

## map和unordered_map

&emsp;&emsp;map是一种有序的容器，底层是用红黑树实现的([什么是红黑树?](https://zhuanlan.zhihu.com/p/31805309)），红黑树是一种自平衡的二叉树，可以保障最坏情况的运行时间，它可以做到O(logn)时间完成查找、插入、删除元素的操作。      
&emsp;&emsp;unordered_map是一种无序的容器，底层是用哈希表实现的([哈希表-维基百科](https://zh.wikipedia.org/wiki/%E5%93%88%E5%B8%8C%E8%A1%A8))，哈希表最大的优点是把数据的查找和存储时间都大大降低。

**直观对比**


|      | map                                                                              | unordered_map                              |
|------|----------------------------------------------------------------------------------|--------------------------------------------|
| 优点 | 1. 有序性，可应用于有顺序要求的应用中     <br>  2. 可保证最坏情况下的运行时间              | 哈希表保证了元素的查找和存储速度都非常的快 |
| 缺点 | 空间占用率高，红黑树的每个节点都需要保存 <br> 父节点、子节点和红黑性质，增加了使用空间 | 哈希表的建立比较耗时                       |


&emsp;&emsp;关于它们的适用场景，在有顺序要求的场合，肯定是要用map的；如果我们只操作一次，为了保证最坏情况下的运行时间，最好也适用map；而如果是需要经常操作，map肯定是没有unordered_map快的。因此，除了有顺序要求和有单词操作时间要求的场景下用map，其他场景都使用unordered_map。


## map的使用方法

&emsp;&emsp;头文件：`include <map>`
&emsp;&emsp;下面的代码中都包含了std：`using namespace std;`
### 创建map对象

```C++
// Method1
map<char, int> map1;
map1['a'] = 88;
map1['b'] = 90;
map1['c'] = 85;

// Method2
map<char, int> map2(map1.begin(), map1.end());

// Method3
map<char, int> map3(map2);
```

### Iterators

Name | Description
-----|---
| begin   | 返回指向迭代器第一个元素的迭代器                   |
| end     | 返回指向迭代器最后一个元素的迭代器                 |
| rbegin  | 返回迭代器逆序第一个元素的迭代器                   |
| rend    | 返回迭代器逆序最后一个元素的迭代器                 |
| cbegin  | 返回常量迭代器的第一个元素的迭代器                 |
| cend    | 返回常量迭代器的最第一个元素的迭代器               |
| crbegin | 返回常量迭代器逆序的第一个元素的迭代器             |
| crend   | 返回常量迭代器逆序的最后一个元素的迭代器           |


```C++
int main() {
    map<char, int>map1;
    map1['a'] = 10;
    map1['b'] = 20;
    map1['c'] = 30;
    
    map<char, int>::iterator it1;
    for (it1 = map1.begin(); it1 != map1.end(); ++it1) {
        cout << it1->first << "=>" << it1->second << endl;
    }

    for (auto it2 = map1.rbegin(); it2 != map1.rend(); ++it2) {
        cout << it2->first << "=>" << it2->second << endl;
    }

    return 0;
}
```

### Capacity

Name | Description
|---------------|--------------------------------------------|
| size          | 返回当前vector使用数据量的大小             |
| max_size      | 返回vector最大可用的数据量                 |
| empty         | 测试vector是否是空的，空返回`true`，否则返回`false`                   |
  
其中max_size跟实际的硬件有关，但也并不是所有的内存空间都可用，下面的代码是在32GB计算机上运行的结果

```C++
cout << map1.size() << endl;               // 3
cout << map1.max_size() << endl;   // 178956970
```


### Element access
&emsp;&emsp;获取元素可以适用`[]`和`at`，如果我们索引的`key`并不在map对象里面，则
 - `[]`会将这个`key`保存到map对象中，对应的`value`是该类型对应的初始化值；
 - `at`会抛出异常


```C++
map1['a'] = 10;
map1['b'] = 20;
map1['c'] = 30;
cout << map1['d'] << endl;            //将map1['d']=0添加到对象中
cout << map1.at('e') << endl;      // 抛出异常
```


### Operations
Name | Description
|---------------|--------------------------------------------|
| find          | 若找到该key,返回该位置的迭代器；否则返回map::end的迭代器           |
| count      |  包含元素计数，返回值为0或1                |
| lower_bound         | 返回指向所取元素的迭代器                 |
| upper_bound          | 返回指向所取元素下一个元素的迭代器           |
| equal_range | 返回一个pair，pair的第一个内容是lower_bound的结果 <br> pair的第二个内容是upper_bound的结果                                                     |

`find`用法如下：
```C++
map<char, int>::iterator it;
it = map1.find('b');
cout << it->second << endl;                  //20
cout << map1.count('a') << endl;      // 1
cout << map1.count('e') << endl;      // 0
```

我们重新定义`map1`为：
```C
map1['a'] = 10;   map1['b'] = 20;   map1['c'] = 30;   map1['d'] = 40;   map1['e'] = 50;
```

下面来测试`lower_bound`和`upper_bound`：
```C++
map<char, int>::iterator itlow, itup;
itlow = map1.lower_bound('b');     // itlow points to b
itup = map1.upper_bound('d');      // itup points to e
map1.erase(itlow, itup);                    // 剩下a 和 e
```
`equal_range`返回的结果同时包含了`lower_bound`和`upper_bound`的结果
```C
map1['a'] = 10;   map1['b'] = 20;   map1['c'] = 30;   map1['d'] = 40;   map1['e'] = 50;
pair<map<char, int>::iterator, map<char, int>::iterator> ret;
ret = map1.equal_range('b');
cout << ret.first->first << "=>" << ret.first->second << endl;                    // b=>20
cout << ret.second->first << "=>" << ret.second->second << endl;      // c=>30 
```



### Modifiers
Name | Description
|---------------|--------------------------------------------|
| insert          | 插入元素           |
| erase      |  删除元素                |
| swap         | 交换两个容器的内容                 |
| clear          | 将容器里的内容清空，size值为0，但是存储空间没有改变           |
| emplace | 插入元素（与insert有区别）|
| emplace_hint | 通过hint position插入元素   |

`insert`就是插入元素，有多种用法
 - 插入某个元素
```C++
map<char, int>map1;
map1['a'] = 10;
map1['b'] = 20;
map1['c'] = 30;
map1.insert(pair<char, int>('d', 40));
```

- 通过hint position插入元素
```C
map<char, int>::iterator it = map1.begin();
map1.insert(it, pair<char, int>('x', 100));
```

 - 插入range
```C++
map<char, int> map2;
map2.insert(map1.begin(), map1.find('c'));
```


`erase`有三种用法：
 - 通过`key`删除某个元素
 
 ```C
 map1.erase('a');
```
 - 通过迭代器删除某个元素
```
it = map1.find('a')
map1.erase(it);
```

 - 删除某个范围内的元素
 ```C
 it = map1.find('c')
 map1.erase(it, map1.end());
```
  
  `swap`用来交换两个map对象的内容
  ```C
  map<char, int> foo, bar;
  foo['x'] = 100;
  foo['y'] = 200;
  bar['a'] = 10;
  bar['b'] = 20;
  bar['c'] = 30;
  foo.swap(bar);
  ```

`clear`用来清空map对象的内容，清空后，size变为0，但实际的存储空间不变
```C
map1.clear();
```

`emplace`也是插入元素，跟`insert`是有区别的，`emplace`没有`insert`的用法多，只能插入一个元素，它是直接在map对象后面创建这个元素，因此速度很快
```C++
map1.emplace('x',100);
map1.emplace('y',200);
```


`emplace_hint`就是在`emplace`的基础上增加了hint position参数
```C
map<char, int> map1;
auto it = map1.end();
it = map1.emplace_hint(it, 'b', 10);
map1.emplace_hint(it, 'a', 20);
map1.emplace_hint(map1.end(), 'c', 30);
```



## unordered_map的使用方法


&emsp;&emsp;头文件：`include <unordered_map>`
&emsp;&emsp;下面的代码中都包含了std：`using namespace std;`,也包含了头文件`#include<string>`
### 创建map对象

```C++
typedef unordered_map<string, int> strIntMap;
strIntMap map1;
strIntMap map2({ {"Tom", 80}, {"Lucy", 85} });
strIntMap map3(map2);
strIntMap map4(map3.begin(), map3.end());
```

### Iterators
由于unordered_map是无序的，因此没有逆序这个说法

Name | Description
-----|---
| begin   | 返回指向迭代器第一个元素的迭代器                   |
| end     | 返回指向迭代器最后一个元素的迭代器                 |
| cbegin  | 返回常量迭代器的第一个元素的迭代器                 |
| cend    | 返回常量迭代器的最第一个元素的迭代器               |

用法跟map是一样的，这里不再举例了



### Capacity


Name | Description
|---------------|--------------------------------------------|
| size          | 返回当前vector使用数据量的大小             |
| max_size      | 返回vector最大可用的数据量                 |
| empty         | 测试vector是否是空的，空返回`true`，否则返回`false`                   |
  
这几个跟map用法也是一样的，不再举例了


### Element access
&emsp;&emsp;获取元素可以适用`[]`和`at`，如果我们索引的`key`并不在map对象里面，则
 - `[]`会将这个`key`保存到map对象中，对应的`value`是该类型对应的初始化值；
 - `at`会抛出异常

跟map用法也是一样的



### Element lookup
Name | Description
|---------------|--------------------------------------------|
| find          | 若找到该key,返回该位置的迭代器；否则返回map::end的迭代器           |
| count      |  包含元素计数，返回值为0或1                |
| equal_range         | 返回指向所取元素的迭代器                 |


这三个函数的用法跟map也是一样的




### Modifiers
Name | Description
|---------------|--------------------------------------------|
| insert          | 插入元素           |
| erase      |  删除元素                |
| swap         | 交换两个容器的内容                 |
| clear          | 将容器里的内容清空，size值为0，但是存储空间没有改变           |
| emplace | 插入元素（与insert有区别）|
| emplace_hint |  通过hint position插入元素  |


`insert`的用法跟map中的`insert`有点区别：
```C++
unordered_map<string, int>stu_score;
unordered_map<string, int> score2 = { {"Lucy", 92 }, {"Tom", 91 } };
pair<string, int> stu1("Lucy", 88);
stu_score.insert(stu1);                                                               // copy insertion
stu_score.insert(make_pair<string, int>("Jim", 96));  // move insertion
stu_score.insert(score2.begin(), score2.end());             // range insertion
stu_score.insert({ {"Hanmei", 99}, {"Lilei", 88} });           // initializer list insertion

```
其他函数跟map用法一样


下面的两组函数都是跟底层的哈希表有关系的

### Buckets
Name | Description
|---------------|--------------------------------------------|
| bucket_count          | 返回bucket的个数           |
| max_bucket_count      |  返回bucket的最大数量                |
| bucket_size         | 返回bucket size                 |
| bucket          |  返回key值所在的bucket序号         |



### Hash_policy
Name | Description
|---------------|--------------------------------------------|
| load_factor          | 返回load factor           |
| max_load_factor      |  设置或返回load factor的最大值                |
| rehash         | 设置bucket的数量                 |
| reserve          |  控制预留空间         |





























