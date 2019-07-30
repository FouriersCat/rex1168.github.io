---
youtube_id: M1FCzIEJc-8
qq_video_id: s08331fzpvr
b_av: 47775635
b_cid: 83685829
b_page: 30
title: Julia科学计算之DataFrames
description: "在Julia中也有DataFrame，用法跟Python中有些类似"
chapter: 12
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 30
---


## 科学计算必备DataFrames

### DataFrames基本操作

跟Python中的pandas的用法很像，相信用过Pandas的朋友上手应该无压力

DataFrame定义

**新建一个DataFrame并增加4列内容**
```
using DataFrames
df1 = DataFrame()
df1[:clo1] = Array([1.0,2.0,3.0])
df1[:clo2] = Array([4.0,5.0,6.0])
df1[:clo3] = Array([7.0,8.0,9.0])
df1[:ID] = Array(['a','b','c'])
show(df1)
>>3×4 DataFrame
│ Row │ clo1    │ clo2    │ clo3    │ ID   │
│     │ Float64 │ Float64 │ Float64 │ Char │
├─────┼─────────┼─────────┼─────────┼──────┤
│ 1   │ 1.0     │ 4.0     │ 7.0     │ 'a'  │
│ 2   │ 2.0     │ 5.0     │ 8.0     │ 'b'  │
│ 3   │ 3.0     │ 6.0     │ 9.0     │ 'c'  │
```

如果没有指定列名，则默认是`x1,x2...`
```
df2 = DataFrame(rand(5,6))
```
![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%971.png)


**在DataFrame定义时直接指定内容**

```
df3 = DataFrame([collect(1:3),collect(4:6)], [:A, :B])
>>	A	B
  Int64	Int64
1	1	4
2	2	5
3	3	6

```

**DataFrame合并**
```
df2 = DataFrame()
df2[:clo1] = Array([11.0,12.0,13.0])
df2[:clo2] = Array([14.0,15.0,16.0])
df2[:clo3] = Array([17.0,18.0,19.0])
df2[:ID] = Array(['a','b','c'])
show(df2)
>>3×4 DataFrame
│ Row │ clo1    │ clo2    │ clo3    │ ID   │
│     │ Float64 │ Float64 │ Float64 │ Char │
├─────┼─────────┼─────────┼─────────┼──────┤
│ 1   │ 11.0    │ 14.0    │ 17.0    │ 'a'  │
│ 2   │ 12.0    │ 15.0    │ 18.0    │ 'b'  │
│ 3   │ 13.0    │ 16.0    │ 19.0    │ 'c'  │
```

合并df1和df2
```
df3 = join(df1, df2, on=:ID)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%972.png)


**列重命名**

```
rename!(df1, :clo1, :cool1)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%973.png)



**索引**

```
df = DataFrame(rand(5,6))
show(df)

5×6 DataFrame
│ Row │ x1        │ x2       │ x3       │ x4       │ x5       │ x6        │
│     │ Float64   │ Float64  │ Float64  │ Float64  │ Float64  │ Float64   │
├─────┼───────────┼──────────┼──────────┼──────────┼──────────┼───────────┤
│ 1   │ 0.678851  │ 0.514764 │ 0.829835 │ 0.996105 │ 0.427939 │ 0.163515  │
│ 2   │ 0.366487  │ 0.834486 │ 0.588859 │ 0.398066 │ 0.45738  │ 0.577453  │
│ 3   │ 0.0945619 │ 0.231903 │ 0.715664 │ 0.538696 │ 0.813534 │ 0.100683  │
│ 4   │ 0.102987  │ 0.269429 │ 0.292984 │ 0.719276 │ 0.756749 │ 0.705312  │
│ 5   │ 0.328692  │ 0.420872 │ 0.545552 │ 0.793303 │ 0.670403 │ 0.0619233 │
```

用索引取值，取2~4列
```
df[2:4]
>>	   x2	       x3	       x4
    Float64	    Float64	    Float64
1	0.514764	0.829835	0.996105
2	0.834486	0.588859	0.398066
3	0.231903	0.715664	0.538696
4	0.269429	0.292984	0.719276
5	0.420872	0.545552	0.793303

```

用名称取值
```
df.x1
>>5-element Array{Float64,1}:
 0.6788506862994854 
 0.366486647281848  
 0.09456191069734388
 0.10298681965872736
 0.32869200293154477
```

取2~4行，x1,x3列
```
df[2:4, [:x1, :x3]]
>>	  x1	      x3
    Float64  	Float64
1	0.366487	0.588859
2	0.0945619	0.715664
3	0.102987	0.292984
```

for循环遍历
```
for row in eachrow(df)
    println(row)
end
```

查看df的描述信息
```
describe(df)
```
![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%974.png)


排序
```
sort!(df, cols = [:x1])
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%975.png)



### DataFrame的csv文件操作

```
using CSV
df = DataFrame(rand(5,6))
show(df)
>>5×6 DataFrame
│ Row │ x1       │ x2       │ x3        │ x4       │ x5        │ x6        │
│     │ Float64  │ Float64  │ Float64   │ Float64  │ Float64   │ Float64   │
├─────┼──────────┼──────────┼───────────┼──────────┼───────────┼───────────┤
│ 1   │ 0.173904 │ 0.981004 │ 0.997001  │ 0.742527 │ 0.816245  │ 0.950086  │
│ 2   │ 0.946748 │ 0.661713 │ 0.0734147 │ 0.181932 │ 0.175604  │ 0.0550761 │
│ 3   │ 0.638618 │ 0.30026  │ 0.926336  │ 0.427942 │ 0.803738  │ 0.481783  │
│ 4   │ 0.156693 │ 0.943436 │ 0.0614211 │ 0.35279  │ 0.0692527 │ 0.417888  │
│ 5   │ 0.351843 │ 0.64204  │ 0.934611  │ 0.910804 │ 0.715309  │ 0.3677    │
```

写文件
```
CSV.write("df123.csv",df)
```

读文件
```
data = CSV.read("df123.csv")
```



### DataFrame中的宏

**@where**
```
using DataFramesMeta
@where(df, :x1 .> 0.2)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%976.png)



**@with**
```
@with(df, :x2 .+ 1)
>>5-element Array{Float64,1}:
 1.9810036040861598
 1.6617132284630372
 1.3002596213068984
 1.9434355174941438
 1.6420398463078156
```

**@select**
```
@select(df, :x3)
>>   x3
    Float64
1	0.997001
2	0.0734147
3	0.926336
4	0.0614211
5	0.934611
```

```
@select(df, p = 2 * :x1, :x2)
>>     p	     x2
    Float64	    Float64
1	0.347808	0.981004
2	1.8935	    0.661713
3	1.27724	    0.30026
4	0.313385	0.943436
5	0.703686	0.64204

```



