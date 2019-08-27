---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Python绘图库matplotlib
description: "."
chapter: 1
category: python-basic
post-headings:
author-link: #
no-video: true
publish-date: 2019-08-26
thumbnail: /static/img/course_cover-small/python.png
index: 4
---






&emsp;&emsp;matplotlib是一个类似于MATLAB的API，上手简单，使用方法跟MATLAB非常类似，兼容LaTeX格式的标题和文档。
调用方法：
```
import matplotlib.pyplot as plt
```
### 绘制list的数据
```
import matplotlib.pyplot as plt
import numpy as np

x1 = [1,2,3,4,5,6]
y1 = [2,4,6,8,10,12]
plt.figure()
plt.plot(x1, y1)
plt.grid(True)
plt.title('list plot demo')
plt.show()
```

<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/list_plot.png?raw=true" width = "350" height = "280" alt="" />

</center>

### 绘制numpy的数据
```
x1 = np.arange(-10, 11)
y1 = x1 ** 2
plt.figure()
plt.plot(x1, y1)
plt.grid(True)
plt.title('numpy plot demo')
plt.show()
```

<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/numpy_plot.png?raw=true" width = "350" height = "280" alt="" />

</center>



###    

&emsp;&emsp;画随机数据
```
x = np.arange(100)
y = np.random.standard_normal((100,))
plt.figure()
plt.plot(x,y.cumsum())
plt.grid(True)
plt.axis('tight')
plt.xlim(0,100)
plt.ylim(np.min(y.cumsum())-1,np.max(y.cumsum())+1))
```

<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/custom.png?raw=true" width = "350" height = "280" alt="" />

</center>





### 设置线条颜色
```
x = np.arange(100)
y = np.random.standard_normal((100,))
plt.figure(figsize=(7,5))
plt.plot(y.cumsum(),'b',lw = 1.5)
plt.plot(y.cumsum(),'ro')
plt.grid(True)
plt.axis('tight')
plt.xlabel('index')
plt.ylabel('value')
plt.title('Set Line Color')
plt.show()
```



<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/color.png?raw=true" width = "350" height = "280" alt="" />

</center>

### 加label
```
y = np.random.standard_normal((30,2)).cumsum(axis = 0)
plt.figure(figsize = (7,5))
plt.plot(y[:,0],lw = 1.5,label = 'y0')
plt.plot(y[:,1],lw = 1.5,label = 'y1')
plt.plot(y,'ro')
plt.grid(True)
plt.legend(loc = 0)
plt.axis('tight')
plt.xlabel('index')
plt.ylabel('value')
plt.title('Add label')
plt.show()
```


<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/label.png?raw=true" width = "350" height = "280" alt="" />

</center>



### 画多个子图
```
plt.figure(figsize = (7,5))
plt.subplot(121)
plt.plot(y[:,0],lw = 2,label = 'y0')
plt.plot(y,'ro')
plt.grid(True)
plt.axis('tight')
plt.xlabel('index')
plt.ylabel('value')
plt.title('subplot1')
plt.subplot(122)
plt.plot(y[:,1],lw = 2,label = 'y1')
plt.plot(y,'ro')
plt.grid(True)
plt.axis('tight')
plt.xlabel('index')
plt.ylabel('value')
plt.title('subplot2')
plt.show()
```


<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/multi_fig.png?raw=true" width = "350" height = "280" alt="" />

</center>




### 散点图
```
plt.figure(figsize = (7,5))
plt.plot(y[:,0],y[:,1],'ro')
plt.grid(True)
plt.xlabel('1st')
plt.ylabel('2nd')
plt.title('scatter plot')
plt.show()
```

<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/scatter.png?raw=true" width = "350" height = "280" alt="" />

</center>

### 双坐标轴
```
y[:,0] = y[:,0] * 100
fig,ax1 = plt.subplots()
plt.plot(y[:,0],'b',lw = 1.5, label = '1st')
plt.plot(y[:,0],'ro')
plt.grid(True)
plt.legend(loc = 0)
plt.axis('tight')
plt.xlabel('index')
plt.ylabel('value 1st')
plt.title('A simple plot')
ax2 = ax1.twinx()
plt.plot(y[:,1],'g',lw = 1.5, label = '2nd')
plt.plot(y[:,1],'ro')
plt.legend(loc = 0)
plt.show()
```

<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/two_axis.png?raw=true" width = "350" height = "280" alt="" />

</center>





### 柱状图+文本标记
```
name_list = ['i7-4790', 'i7-6700k', 'i7-7700k', 'E5-1680 v4', 'E5-2618L v3', 'i7-5700EQ']
App_acquisition_list = [3, 3, 3, 3, 3, 2.6]
App_record_list = [1.064, 1.135, 3, 3, 3, 1.7]
CPU_usage_acquis_list = [29, 17, 23, 12, 11, 22]
CPU_usage_record_list = [23, 7, 19, 9, 14, 30]
total_width = 2.5
width = total_width/len(name_list)
x = np.arange(len(name_list))
# CPU usage
fig = plt.figure()
ax = plt.subplot()
rects = ax.bar(list(x), CPU_usage_acquis_list, width = width, label = 'App采集时CPU使用率', tick_label = name_list, fc = 'y')

for r,i in zip(rects,range(6)):
    ax.text(r.get_x()+0.22, r.get_height() + 1.5, '%d' % CPU_usage_acquis_list[i], ha = 'center', va = 'top')

x = x + width
rects = ax.bar(x, CPU_usage_record_list, width = width, label = 'App存盘时CPU使用率', fc = 'r')
for r,i in zip(rects,range(6)):
    ax.text(r.get_x()+0.22, r.get_height() + 1.5, '%d' % CPU_usage_record_list[i], ha = 'center', va = 'top')

tick_loc = list(x - width/2)
ax.set_xticks(tick_loc)
plt.ylim(0, 38)
plt.ylabel('CPU使用率(百分比)')
plt.title('CPU使用率情况比较')
plt.legend()
plt.show()
```


<center>
<img src="https://github.com/Bounce00/pic/blob/master/python/bar.png?raw=true" width = "350" height = "240" alt="" />

</center>










