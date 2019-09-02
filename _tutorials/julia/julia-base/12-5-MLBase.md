---
youtube_id: 1s03xE-pBq4
qq_video_id: w0833l17oku
b_av: 61523729
b_cid: 106651342
b_page: 11
title: Julia机器学习之MLBase
description: "Julia机器学习之MLBase"
chapter: 12
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 34
---


### MLBase

```
using MLBase
```
机器学习基础库，不包含任何机器学习的算法，但为机器学习提供很多的必要工具，比如Cross validation等


先来看下MLBase中几个做简单数据处理的函数
```
repeach(1:3, 2)
>>6-element Array{Int64,1}:
 1
 1
 2
 2
 3
 3
```

```
repeach(["a", "b", "c"], 2)
>>6-element Array{String,1}:
 "a"
 "a"
 "b"
 "b"
 "c"
 "c"
 ```
 
```
repeach(["a", "b", "c"], [1, 2, 3])
>>6-element Array{String,1}:
 "a"
 "b"
 "b"
 "c"
 "c"
 "c"
```

```
A = reshape(collect(1:6), 2, 3)
repeachcol(A, 2)
>>2×6 Array{Int64,2}:
 1  1  3  3  5  5
 2  2  4  4  6  6
```

```
repeachrow(A, 2)
>>4×3 Array{Int64,2}:
 1  3  5
 1  3  5
 2  4  6
 2  4  6
```


```
labels = labelmap(['a', 'b', 'b', 'c', 'c', 'c'])
>>LabelMap (with 3 labels):
[1] a
[2] b
[3] c
```

```
labelencode(labels, ['b', 'c', 'c', 'a'])
>>4-element Array{Int64,1}:
 2
 3
 3
 1
```



