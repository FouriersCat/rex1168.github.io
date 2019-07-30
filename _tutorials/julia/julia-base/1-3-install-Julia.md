---
youtube_id: y9jbF9whZJE
qq_video_id: r08307w41sw
b_av: 47775635
b_cid: 83681145
b_page: 3
title: Julia的安装
description: "3种方式安装使用Julia"
chapter: 1
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 3
---

# 安装Julia

 [Julia官网](https://julialang.org/)
 
 现在已经出了1.0.3版本，直接现在安装即可，安装完成后打开julia，可看到如下的界面：
 
 (Windows)
 
![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/1-2.png)
 
 
 
 
 
 
 
 
 
 
 
 (MAC)
 
![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/1-3.png)
 
 但我们写程序时如果只用REPL太蛋疼，调试也不方便，还需要Julia的运行工具：






 下图是官方给出的Julia的Editor和IDE
 
![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/1-4.png)
 
 
 
 
 
 
 
 
 
 
 
 
 
 在我们的教程中，会给出下面三种方式的安装教程：
 - Julia Pro（基于Atom，算是比较正式的IDE了）
 - Jupyter notebook
 - vscode的Julia插件
 
**Julia Pro安装**

[官网](https://juliacomputing.com/products/juliapro.html)
下载之后直接安装即可，Julia Pro安装的内容比较全，基本算是Julia的全家桶了，Jupyter、REPL和基于Atom的IDE都有，目前最新的也是v1.0.2。装完之后即可直接使用。

**vscode Julia插件**

在vscode中安装Julia插件和Code Runner插件，在User Settings中设置
```
"julia.executablePath": "D:\\MyProgramFile\\Julia-1.0.0\\bin\\julia.exe"
```
完成Julia文件后，直接右键Run Code即可。
在MAC上的vscode上，还不支持Julia 1.0版本，因此还不能运行1.0的Julia。相信不久就会出来了，大家耐心等待。等出来了第一时间会在公众号“从数据分析到量化投资”里通知大家。

**Jupyter note**

 - 先安装Jupyter，对于已经安装过Anaconda的朋友来说，已经默认安装了jupyter
 - 运行Julia的REPL，按`]`键进入Pkg模式
 




 

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/1-5.png)

 - 运行`add IJulia`，安装IJulia
 - 运行`using IJulia`,这时如果提示我们需要run Pkg.build("ZMQ")时，我们根据提示内容操作
 - 在julia模式下先导入Pkg，`using Pkg`，再运行`Pkg.build("ZMQ")`,这是时会自动下载安装GitHub上各种需要的文件，但经常会提示各种下载错误，需要我们根据提示的错误，找到相应的网址和文件，下载下来后放到提示的文件夹中，比如`ZMQ.x86_64-w64-mingw32.tar.gz`文件，放到`~\.julia\packages\ZMQ\ABGOx\deps\usr\downloads`目录`（~是\home目录，Windows上在C:\User\UserName下,MAC上在User\UserName下)`，再次运行`Pkg.build("ZMQ")`；如果提示不止"ZMQ"这一个package，我们只需要根据提示安装所有下载失败的文件即可。
 - 在安装过程中可能会提示需要安装`Windows Management Framework 3.0`以上版本，我们也找到最新的版本安装即可
 - 如果报的error中，需要运行`Pkg.build("IJUlia")`，那我们运行前要先把jupyter.exe添加进来，运行`ENV["JUPYTER"]=“D:\\Anaconda3\\Scripts\\jupyter.exe"`
 - 没有error后，运行`using IJulia`
 - 运行`IJulia.notebook()`即可打开支持1.0版本的Jupyter notebook

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/1-6.png)






还有另外一种解决方案就是解决“根”上的问题：加速访问GitHub的速度。

这里给大家提供一个csdn上的参考方案

[GitHub下载速度加速](https://blog.csdn.net/u012885320/article/details/80925991)

mac上刷新dns：

   sudo dscacheutil -flushcache
   
  sudo killall -HUP mDNSResponder
  
  say DNS cache flushed
  
  当听到语音说：dns cache flushed，说明成功刷新了

大家可以根据自己的情况，选择适合自己的方案。





