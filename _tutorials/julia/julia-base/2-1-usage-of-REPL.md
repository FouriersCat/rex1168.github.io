---
youtube_id: Eq59qfI_5Ns
qq_video_id: d08308c3q2m
b_av: 61523729
b_cid: 106752558
b_page: 28
title: Julia的REPL的使用
description: "REPL的有几种工作模式？Julia中怎么安装或删除Package?"
chapter: 2
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 4
---


# 开始使用Julia

***注意***
目前网上的很多教程都是0.3版本，大家在学习的时候要区分一下，0.3版本的有些语法在1.0中已经不能使用

在Julia的REPL下，有多种模式：
- help模式，按`?`进入help模式
- shell模式，按`;`进入shell模式
- package模式，按`]`进入package模式
- 按`backspace`键返回正常Julia模式

也可以在REPL中运行一些简单的运算，或者定义函数等

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%20REPL%20%E5%8F%98%E9%87%8F1.png)


在help模式下，可以查看各种帮助文档，就相当于执行`@doc `命令；

在package模式下，可以增加、删除各种库，相当于用Pkg这个模块来操作；
package模式下的`add GR`相当于`Pkg.add("GR")`
package模式下的`rm GR`相当于`Pkg.rm("GR")`

其他关于package的操作
```
Pkg.installed()  #查看已安装的库和它们的版本
Pkg.update()     #升级安装的库
```

我们在julia的目录下新建一个helloworld.jl的文件，里面内容为：
```julia
println("Hello World!")
```
在Windows中，shell模式下，执行`julia helloworld.jl`，即可运行该文件。

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%20REPL%20%E5%8F%98%E9%87%8F2.png)






