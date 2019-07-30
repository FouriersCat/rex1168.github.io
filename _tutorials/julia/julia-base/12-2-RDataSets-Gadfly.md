---
youtube_id: 5JnOywkU9EI
qq_video_id: o0833lvt2o7
b_av: 47775635
b_cid: 83685887
b_page: 30
title: Julia科学计算之RdataSets
description: "最常用的科学计算数据包：RdataSets以及科学计算绘图工具Gadfly"
chapter: 12
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 30
---





## RDatasets 

RDatasets是Julia中的一个数据集，里面包含了很多可以学习和验证的数据，其中就包括iris数据集。

iris数据集介绍

在机器学习领域，有大量的公开数据集。iris就是其中非常重要的一个。

Iris Data Set（鸢尾属植物数据集）是一个历史很悠久的数据集，它首次出现在著名的英国统计学家和生物学家Ronald Fisher 1936年的论文《The use of multiple measurements in taxonomic problems》中，被用来介绍线性判别式分析。在这个数据集中，包括了三类不同的鸢尾属植物：Iris Setosa，Iris Versicolour，Iris Virginica。每类收集了50个样本，因此这个数据集一共包含了150个样本。

该数据集测量了所有150个样本的4个特征，分别是：

 - sepal length（花萼长度）
 - sepal width（花萼宽度）
 - petal length（花瓣长度）
 - petal width（花瓣宽度）
 
以上四个特征的单位都是厘米（cm）。

通常使用m表示样本量的大小，n表示每个样本所具有的特征数。

```
using RDatasets
```
```
iris = dataset("datasets", "iris")
```

```
features = convert(Array, iris[:, 1:4])
labels = convert(Array, iris[:, 5])
```


## Gadfly绘图


```
p = plot(iris, x=:SepalLength, y=:SepalWidth, Geom.point)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%977.png)


把图片输出到文件
```
img = SVG("iris_plot.SVG", 10cm, 8cm)
draw(img, p)
```


用线连接起来
```
p = plot(iris, x=:SepalLength, y=:SepalWidth, Geom.point, Geom.line)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%978.png)


以Species作为颜色依据
```
p = plot(iris, x=:SepalLength, y=:SepalWidth, Geom.point, color=:Species)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%979.png)


在图中加上lable
```
p = plot(iris, x=:SepalLength, y=:SepalWidth, label=:Species,  color=:Species, Geom.point, Geom.label)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%9710.png)

柱状图
```
fig1a = plot(iris, x="SepalLength", y="SepalWidth", Geom.point)
fig1b = plot(iris, x="SepalWidth", Geom.bar)
fig1 = hstack(fig1a, fig1b)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%9711.png)


