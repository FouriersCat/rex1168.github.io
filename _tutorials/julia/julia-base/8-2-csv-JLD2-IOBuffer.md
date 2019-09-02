---
youtube_id: 7DpUTV6Wkm4
qq_video_id: s0833wzbcri
b_av: 61523729
b_cid: 106991738
b_page: 46
title: csv文件 JLD2文件 IOBuffer
description: "csv文件 JLD2文件 IOBuffer"
chapter: 8
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 22
---


## csv文件

写csv文件与操作普通文件类似，中间加个逗号就可以了。
```
using DelimitedFiles
data = rand(Int8,10,5)
writedlm("file1.csv", data, ',')
```

读文件时，我们可以使用DataFrames来读csv文件。
关于DataFrames我们将在科学计算一节中讲到。
```Julia
using DataFrames
df = readtable("file1.csv")
```

也可以使用CSV package自带的函数读取
```
using CSV
df1 = CSV.read("file1.csv")
```
用CSV.read()读上来的数据也是DataFrame格式。






##   JLD2

JLD2是JLD格式的改进，也是一种HDF5格式。

**写文件**
```Julia
using JLD2
stri = "hello world"
fid = jldopen("jld_test.jld2","w")
write(fid, "stri", stri)
close(fid)
```

**读文件**
```
fid = jldopen("jld_test.jld2","r")
res = read(fid,"ss")
```


## IOBuffer
相当于在内存中创建一个IOStream
```
io = IOBuffer()
write(io, "Hello World!", " JuliaLang is great!")
```
采用`take!`的方式将内容从IOBuffer中读出
```Julia
String(take!(io))
```
如果在创建IOBuffer时就指定了里面的内容，则词IOBuffer为只读的。
```Julia
io = IOBuffer("Hello World!")
write(io, "This is not writable.")
read(io, String)
```
关闭IOBuffer
```Julia
close(io)
```

