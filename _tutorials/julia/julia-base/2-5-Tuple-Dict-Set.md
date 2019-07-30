---
youtube_id: _CLn7nElako
qq_video_id: a0830ae0lyc
b_av: 47775635
b_cid: 83682507
b_page: 8
title: Tuple Dict Set
description: "Python中有的数据类型，Julia中也有"
chapter: 2
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 8
---


## Tuple

用`()`表示，内容不可更改
```Julia
x1 = (1,2,3,4)
x1[1] # 序号从1开始
length(x1)
x1[2:end]
x2 = (1, 2.4, 'a', "hello")
x3 = (a=1, b = 2)
x3.a
>>1
```

## 字典
Julia中也支持字典类型
```Julia
dic = Dict("aa" => 1, "bb" => 2, "cc" => 3)
typeof(dic)
>>Dict{String,Int64}
dic.count
dic.keys
dic.vals
dic["aa"]
```

也可以把Array转成字典
```
ary = ["one" => 1, "two" => 2]
Dict(ary)
```

把Array中的Tuple转成字典
```Julia
tpl = [("one", 1), ("two", 2)]
Dict(tpl)
```

把两个Array组合成Dict
```Julia
vas = [1, 2, 3]
kes = ["one", "two", "three"]
Dict(zip(kes, vas))
```

新建一个空字典
```Julia
d = Dict()
d["one"] = 1
```

在新建空字典时指定类型
```Julia
d = Dict{String,Int64}()
```

字典遍历
```
d = Dict("one" => 1, "two" => 2, "three" => 3)
for (k,v) in d
    println("value is $k for key $v")
end

for v in values(d)
    println(v)
end
```

字典的其他用法
```JUlia
d = Dict("one" => 1, "two" => 2)
get(d, "three", -1)
get(d, "one", -1)
delete!(d, "one")

# 使用fieldnames查看该类型的fieldname
fieldnames(typeof(d))
```

## Set
```Julia
S = Set([1, 2, 3, 2])
S = Set(1:3:10)
2 in S
4 in S
issubset([1,4], S)  # [1,4] ⊆ S

A = Set([1, 2, 3, 4])
B = Set([2, 4, 6, 7])
# A 交 B
intersect(A,B)

# A 或 B
union(A, B)

# 注：不是Set也可以进行交/或操作，但返回不是Set
interset([1,2,3,4], [2,4,6,7])
```

