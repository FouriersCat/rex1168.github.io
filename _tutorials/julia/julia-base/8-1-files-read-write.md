---
youtube_id: Gg494WbMgYw
qq_video_id: i0833monio6
b_av: 47775635
b_cid: 83684494
b_page: 21
title: Julia文件读写
description: "Julia文件读写"
chapter: 8
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 21
---

# IO操作



在对文件的操作中，如果我们未指定文件的路径，则默认的路径为
 - 在使用Atom或vscode时，默认路径即为.jl文件的目录
 - 在使用jupyter notebook时，windows系统默认路径为'C:\Users\用户名\';MAC的目录在jupyter中可以看到
 
 ![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%20IO1.png)

 - 在使用REPL时，系统默认路径为Julia的安装目录，可以执行`pwd()`命令可查看当前的工作路径，，如果想更换路径，使用`cd(dir::String)`命令，若想跟换回原始路径，使用`homedir()`命令



## 读文件操作
```Julia
s1 = readlines("sdy.txt")
s2 = open("sdy.txt","r")
line = readline(s2)
eof(s2)
position(s2)
read(s2,Char)
line = readline(s2)
read(s2,Int8)
close(s1)
```
在REPL中，可以查看open的用法，在open中，还可以调用函数
```Julia
open(readline, "sdy.txt")
```
当然，也可以调用自定义的函数
```Julia
readFunc(s::IOStream) = read(s, Char)
open(readFunc, "sdy.txt")
```
还可以像Python中的`with open...as`的用法一样打开文件
```Julia
open("sdy.txt","r") do stream
    for line in eachline(stream)
        println(line)
    end
end
```
也可以将上述过程定义成函数再用open操作
```Julia
function readFunc2(stream)
    for line in eachline(stream)
        println(line)
    end
end
open(readFunc2, "sdy.txt")
```



## 写文件操作
```Julia
write("wrFile1.txt", "Hello World")
open("wrFile2.txt","w") do f
    for ch in 'a':'z'
        write(f, ch)
    end
    write(f,'\n')
end
```


open函数中的参数模式定义如下

Mode | Description 
-----|------------
  r  | read
  w  | write, create, truncate
  a  | write, create, append
  r+ | read, write
  w+ | read, write, create, truncate	
  a+ | read, write, create, append
  
```
io = open("wrFile3.txt","w")
write(io, "Write File...\n")
```

但这种读写文件的方式对矩阵操作支持不太好
```Julia
mat = reshape(1:9, (3,3))
write("mat.txt", mat)
```

最好使用`DelimitedFiles`包进行矩阵的读写操作
```Julia
using DelimitedFiles
m = reshape(1:9, (3,3))
writedlm("number.txt", m, '\t')

cont = readdlm("number.txt")
```

除了txt格式外，Julia还可以对csv等格式，但Julia自带的数据序列化工具JLD2速度速度更快，扩平台，重点推荐。



