---
youtube_id: H7JLJcoMJeg
qq_video_id: g0833mcpr6e
b_av: 47775635
b_cid: 83686848
b_page: 39
title: Julia机器学习之Kmeans
description: "Julia机器学习之Kmeans"
chapter: 12
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 39
---


### K-means


```
using Clustering
using RDatasets
iris = dataset("datasets", "iris")
features = convert(Array, iris[:, 1:4])
labels = convert(Array, iris[:, 5]);
```

```
# choose 3 random starting points
initseeds(:rand, convert(Matrix,features'), 3)
>>3-element Array{Int64,1}:
  48
  80
 114
```


```
result = kmeans(features, 3)
>>KmeansResult{Float64}([5.1 2.45 0.2; 4.9 2.2 0.2; … ; 6.2 4.4 2.3; 5.9 4.05 1.8], [1, 2, 2, 3], [3.63798e-12, 166.128, 166.127, 0.0], [1, 2, 1], [1.0, 2.0, 1.0], 332.2550000000019, 2, true)
```

```
using Gadfly
plot(iris,x="PetalLength",y="PetalWidth",color=result.assignments, Geom.point)
```


![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%9718.png)




