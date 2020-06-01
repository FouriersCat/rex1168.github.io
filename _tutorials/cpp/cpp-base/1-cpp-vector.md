---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: C++ vector用法详解
description: ""
chapter: 1
category: cpp-base
post-headings:
author-link: #
no-video: true
publish-date: 2020-05-30
thumbnail: /static/img/course_cover-small/cpp.png
index: 1
---



### vector概述

&emsp;&emsp;vector是种容器，类似数组一样，但它的size可以动态改变。

&emsp;&emsp;vector的元素在内存中连续排列，这一点跟数组一样。这意味着我们元素的索引将非常快，而且也可以通过指针的偏移来获取vector中的元素。

&emsp;&emsp;但连续排列也带来了弊端，当我们向vector中间插入一个数据时，整个vector的size变大，在内存中就需要重新分配空间，常规的做法是直接申请一个新的array，并将所有元素拷贝过去；但这么做的话，无疑太浪费时间，因此vector采用的做法是：vector会分配额外的空间，以适应size的动态增长。因此，包含同样数量元素的vector和数组相比，占用的空间会更大。而且在vector最后增加或者删除一个元素，消耗的时间是一个常数值，与vector的size无关。

&emsp;&emsp;与其他容器（deques、lists、forward_lists）相比，vector在获取元素和对最后一个元素的操作效率上更高；但对于中间元素的操作，性能则相对较差。


### vector的使用

#### 头文件
```
#include <vector>
```

#### 创建vector对象
```
std::vector<int> vec1;                                // 空的vector，数据类型为int
std::vector<int> vec2(4);                             // 4个值为0的vector
std::vector<int> vec3 (4,10);                         // 4个值为10的vector [10 10 10 10]
std::vector<int> vec4 (vec3.begin(),vec3.end());      // [10 10 10 10]
std::vector<int> vec5 (vec3);                         // [10 10 10 10]
std::vector<int> vec6 = {10, 20, 30, 40};             // [10 20 30 40]
```



#### 属性及操作


**Iterators**

Name | Description
-----|---
| begin   | 返回指向迭代器第一个元素的指针                   |
| end     | 返回指向迭代器最后一个元素的指针                 |
| rbegin  | 返回迭代器逆序第一个元素的指针                   |
| rend    | 返回迭代器逆序最后一个元素的指针                 |
| cbegin  | 返回常量迭代器的第一个元素的指针                 |
| cend    | 返回常量迭代器的最第一个元素的指针               |
| crbegin | 返回常量迭代器逆序的第一个元素的指针             |
| crend   | 返回常量迭代器逆序的最后一个元素的指针           |

<br/>
<br/>

**Capacity**

Name | Description
|---------------|--------------------------------------------|
| size          | 返回当前vector使用数据量的大小             |
| max_size      | 返回vector最大可用的数据量                 |
| resize        | 调整vector中的元素个数                     |
| capacity      | 返回vector中总共可以容纳的元素个数         |
| empty         | 测试vector是否是空的                       |
| reserve       | 控制vector的预留空间                       |
| shrink_to_fit | 减少capacity到size的大小                   |

<br/>
<br/>

**Element access**


Name | Description
|---------------|---------------------------------------------------------------------|
| operator[]    | 在[]中可以做运算                            |
| at            | vector.at(i)相当于vector[i]                 |
| front         | 返回第一个元素的值                          |
| back          | 返回最后一个元素的值                        |
| data          | 返回指向vector内存数据的指针                |

<br/>
<br/>


**Modifiers**

Name | Description
|---------------|---------------------------------------------------------------------|
| assign        | 指定vector内容                              |
| push_back     | 在容器的最后一个位置插入元素x               |
| pop_back      | 删除最后一个元素                            |
| insert        | 插入元素                                    |
| erase         | 擦除元素                                    |
| swap          | 交换两个容器的内容                          |
| clear         | 将容器里的内容清空，size值为0，但是存储空间没有改变                            |
| emplace       | 插入元素（与insert有区别）                  |
| emplace_back  | 在容器的最后一个位置插入元素x(与push_back有区别)   |



<br/>
<br/>


**Allocator**

Name | Description
|---------------|---------------------------------------------------------------------|
| get_allocator | 返回vector的内存分配器                      |


<br/>
<br/>


### 具体使用方法

**Iterators**

&emsp;&emsp;Iterators使用方式比较简单，下面的程序也很直观的展现了它们的用法。其中`it3`和`it4`在`for`中使用`auto`来定义，使用更加方便。

```
#include <iostream>
#include <vector>

using namespace std;

int main() {
    vector<int> vec0;

    for (int i = 0; i < 10; ++i) {
        vec0.push_back(i);            //[0,1,2,3,4,5,6,7,8,9]
    }
    
    vector<int>::iterator it1;
    for (it1 = vec0.begin(); it1 != vec0.end(); ++it1) {
        cout << ' ' << *it1 << endl;   //[0,1,2,3,4,5,6,7,8,9]
    }

    vector<int>::reverse_iterator it2;
    for (it2 = vec0.rbegin(); it2 != vec0.rend(); ++it2) {
        cout << ' ' << *it2 << endl;   //[9,8,7,6,5,4,3,2,1,0]
    }
    
    for (auto it3 = vec0.cbegin(); it3 != vec0.end(); ++it3) {
        cout << ' ' << *it3 << endl;    //[0,1,2,3,4,5,6,7,8,9]
    }

    for (auto it4 = vec0.crbegin(); it4 != vec0.crend(); ++it4) {
        cout << ' ' << *it4 << endl;    //[9,8,7,6,5,4,3,2,1,0]
    }

    return 0;
}
```

&emsp;&emsp;如果是vec0.begin()+1,则表示从第二个元素开始。

**Capacity**

&emsp;&emsp;Capacity中的几个函数，从描述上看有点模糊，下面我们具体解释一下。

&emsp;&emsp;
 - size返回的是当前vector中有多少元素；
 - max_size返回的是最大可用的数据量，这跟实际的硬件有关，但也并不是所有的内存空间都可用，比如下面程序中的运行的内存大小为32GByte,但返回的结果是4GByte；
 - capacity是当前vector分配的可以容纳的元素个数，下面的代码中，vec0可以容纳13个元素，但仅包含了size(即10)个元素，还有3个元素可以放进去，当再放入超过3个元素后，vec0就会被重新分配空间；**所以，capacity始终大于等于size**；
 - resize把容器改为容纳n个元素。调用resize之后，size将会变为n；其中n又分了三种情况，当n<size时，容器尾部的元素会被销毁，capacity保持不变；当size<n≤capacity时，新增加的元素都是0，capacity保持不变；当n>capacity时，size和capacity同时变为n，新增加的元素都是0；
 - empty比较简单，当vector为空时，返回1，不为空返回0；
 - shrink_to_fit，去掉预留的空间，capacity与size保持一致

```
#include <iostream>
#include <vector>

using namespace std;

int main() {
    vector<int> vec0;
    cout << "vec0 empty status:" << vec0.empty() << endl;   // 1

    for (int i = 0; i < 10; ++i) {
        vec0.push_back(i);            //[0,1,2,3,4,5,6,7,8,9]
    }

    cout << "vec0 empty status:" << vec0.empty() << endl;   // 0

    //---------------Capacity--------------------------
    cout << "vec0 size:" << vec0.size() << endl;          //10
    cout << "vec0 max size:" << vec0.max_size() << endl;  //1073741823 = 4GByte
    cout << "vec0 capacity:" << vec0.capacity() << endl;  //13
    vec0.resize(20);
    cout << "new size:" << vec0.size() << "  new capacity:" << vec0.capacity() << endl;  //20 20
    vec0.resize(5);
    cout << "new size:" << vec0.size() << "  new capacity:" << vec0.capacity() << endl;  // 5 20
    vec0.shrink_to_fit();
    cout << "new size:" << vec0.size() << "  new capacity:" << vec0.capacity() << endl;  // 5 5

    return 0;
}
```


 - reserve用来指定vector的预留空间，在上面的代码中，没有resize前capacity是值为13，但我们可以提前指定vector的容量。

```
#include <iostream>
#include <vector>

using namespace std;

int main() {
    //---------------reserve-----------------------------
    std::vector<int> vec0;
    int sz;
    sz = vec0.capacity();
    std::cout << "making vec0 grow:\n";
    for (int i = 0; i < 100; ++i) {
        vec0.push_back(i);
        if (sz != vec0.capacity()) {
            sz = vec0.capacity();
            std::cout << "capacity changed: " << sz << '\n';
        }
    }

    std::vector<int> vec1;
    sz = vec1.capacity();
    vec1.reserve(100);   // this is the only difference with vec0 above
    std::cout << "making vec1 grow:\n";
    for (int i = 0; i < 100; ++i) {
        vec1.push_back(i);
        if (sz != vec1.capacity()) {
            sz = vec1.capacity();
            std::cout << "capacity changed: " << sz << '\n';
        }
    }
    return 0;
}
```

打印结果为：
```
making vec0 grow:
capacity changed: 1
capacity changed: 2
capacity changed: 3
capacity changed: 4
capacity changed: 6
capacity changed: 9
capacity changed: 13
capacity changed: 19
capacity changed: 28
capacity changed: 42
capacity changed: 63
capacity changed: 94
capacity changed: 141
making vec1 grow:
capacity changed: 100
```

**Element access**

&emsp;&emsp;vector元素获取的使用方法也比较简单，下面的代码可以很好的展示：
```
vector<int> vec0;
for (int i = 0; i < 10; ++i) {
    vec0.push_back(i);            //[0,1,2,3,4,5,6,7,8,9]
}

for (unsigned int i = 0; i < vec0.size(); ++i) {
        cout << vec0.at(i) << endl;       //[0,1,2,3,4,5,6,7,8,9]
    }

    cout << "front element:" << vec0.front() << endl;  //0
    cout << "back element:" << vec0.back() << endl;    //9

    vector<int>vec1(5);             // [0,0,0,0,0]
    int *p = vec1.data();           // vec1 must not be empty
    for (unsigned int i = 0; i < vec1.size(); ++i) {
        *p = i;
        ++p;
    }
    for (unsigned int i = 0; i < vec1.size(); ++i) {
        cout << vec1[i] << endl;    // [0,1,2,3,4]
    }

```


**Modifiers**

&emsp;&emsp;assign主要有三种用法，如下面的demo所示：

```
vector<int> vec1;
vector<int> vec2;
vector<int> vec3;

vec1.assign(5, 10);               //[10,10,10,10,10]

vector<int>::iterator it;
it = vec1.begin() + 1;

vec2.assign(it, vec1.end() - 1);  //[10,10,10]

int arr[] = { 10,20,30,40};
vec3.assign(arr, arr + 4);        //[10,20,30,40]
```

&emsp;&emsp;`push_back`和`pop_back`用法比较简单
```
vec0.push_back(55);
cout << "Last element:" << vec0.back() << "  size: " << vec0.size()<< endl;  //55 11
vec0.pop_back();
cout << "Last element:" << vec0.back() << "  size: " << vec0.size() << endl; //9 10
```



&emsp;&emsp;在vector中，有插入元素功能的函数有四个：`push_back`、`insert`、`emplace`和`emplace_back`，其中`push_back`上面讲了，`emplace_back`是在C++11中引入的，用法跟`push_back`完全一样，都是在vector的最后插入一个元素。
```
vec3.emplace_back(100);
```
那为什么要引入一个用法完全一样的函数？因为它们的底层实现方式不同，`push_back`向容器尾部添加元素时，将这个元素拷贝或者移动到容器中（如果是拷贝的话，事后会自行销毁先前创建的这个元素）；而 `emplace_back` 在实现时，则是直接在容器尾部创建这个元素，省去了拷贝或移动元素的过程。

&emsp;&emsp;`insert`和`emplace`(C++11中引入)都可以向vector中间插入元素，但`insert`可以插入多个元素，`emplace`一次只能插入一个元素。

&emsp;&emsp;`insert`有三种用法：    
1. 在指定位置loc前插入值为val的元素,返回指向这个元素的迭代器,
2. 在指定位置loc前插入num个值为val的元素
3. 在指定位置loc前插入区间[start, end)的所有元素 

```
// method1
vector<int> vec1;
int arr[] = { 1,2,3,4,5 };
vec1.insert(vec1.begin(), arr, arr + 5);     //[1,2,3,4,5]

// method2
vector<int> vec2(vec1); 
vector<int>::iterator it = vec1.begin() + 2;
vec1.insert(it, 3, 20);                      // [1,2,20,20,20,3,4,5]

// method3
vector<int> vec3(3, 40);
vector<int>::iterator it2 = vec2.begin();
vec2.insert(it2, vec3.begin(), vec3.end());  // [40,40,40,1,2,3,4,5]
```

&emsp;&emsp;`emplace`的使用方式为：
```
iterator emplace (const_iterator pos, args...);
```

```
vector<int> vec0{1,2,3};
vec0.emplace(vec0.begin()+2,10);    //[1,2,10,3]
```

&emsp;&emsp;若都是插入一个元素的情况下，该使用哪个函数呢？当然是C++11中新引入的`emplace`，`emplace`在插入元素时，在指定位置直接构造元素，而`insert`是生成元素，再将其赋值或移动到容器中。



&emsp;&emsp;vector中有三种可以删除元素的操作，第一种就是我们上面讲到的`pop_back`，删除最后一个元素，无返回值；第二种是`clear`，将容器清空，size变为0，无返回值；第三种是`erase`，通过迭代器来删除元素，可以删除一个元素，也可以删除某个范围内的元素，返回下一个位置的迭代器。

```
vector<int> vec0{ 1,2,3,4,5,6,7,8,9,10};
//for (int i = 0; i < 4; ++i) {
//  vec0.pop_back();
//}
vec0.erase(vec0.begin() + 3);               // [1,2,3,5,6,7,8,9,10]
vec0.erase(vec0.begin(), vec0.begin() + 5); // [7,8,9,10]
//vec0.clear();
```

&emsp;&emsp;`swap`的用法也比较简单，就是交换两个vector的内容
```
vector<int> vec1{ 1,2,3,4,5 };
vector<int> vec2{ 10,20,30 };
vec1.swap(vec2);
```

**Allocator**

&emsp;&emsp;`get_allocator`用的不是特别多，我们把使用方法讲一下。

```
vector<int> myvector;
int * p;
unsigned int i;

// 使用Allocator为数组分配5个元素的空间
p = myvector.get_allocator().allocate(5);


for (i=0; i<5; i++) myvector.get_allocator().construct(&p[i],i);

std::cout << "The allocated array contains:";
for (i=0; i<5; i++) std::cout << ' ' << p[i];    //[0,1,2,3,4]


for (i=0; i<5; i++) myvector.get_allocator().destroy(&p[i]);
myvector.get_allocator().deallocate(p,5);
```


## 二维数组


&emsp;&emsp;上面我们讲的，都相当于是一维数组，vector还支持二维数组，但这种二维数组是通过嵌套的方式来实现，并不像Python或者Matlab的矩阵那么直观。

```
vector<vector<int>> arr(3);
for (int i = 0; i < 3; ++i) {
    arr[i].resize(3);           //3x3 array
}
for (int i = 0; i < 3; ++i) 
    for (int k = 0; k < 3; ++k) 
        arr[i][k] = i * k;      // 赋值
arr.resize(4);                  // 二维数组包含4个变量
arr[2].resize(5);               // 第3个变量包含5个变量
```








