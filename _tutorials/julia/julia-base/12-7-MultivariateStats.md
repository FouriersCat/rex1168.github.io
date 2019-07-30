---
youtube_id: n3j7dHFUZdI
qq_video_id: v0833baqkm3
b_av: 47775635
b_cid: 83686495
b_page: 36
title: Julia机器学习之MultivariateStats
description: "Julia机器学习之MultivariateStats"
chapter: 12
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 36
---


### MultivariateStats


PCA（Principal Component Analysis）是一种常用的数据分析方法。PCA通过线性变换将原始数据变换为一组各维度线性无关的表示，可用于提取数据的主要特征分量，常用于高维数据的降维。

```
using MultivariateStats, RDatasets 

# load iris dataset
iris = dataset("datasets", "iris")

# split half to training set
Xtr = convert(Array,(iris[1:2:end,1:4]))'
Xtr_labels = convert(Array,(iris[1:2:end,5]))

# split other half to testing set
Xte = convert(Array,(iris[2:2:end,1:4]))'
Xte_labels = convert(Array,(iris[2:2:end,5]))

# suppose Xtr and Xte are training and testing data matrix,
# with each observation in a column

# train a PCA model, allowing up to 3 dimensions
M = fit(PCA, Xtr; maxoutdim=3)

# apply PCA model to testing set
Yte = transform(M, Xte)

# reconstruct testing observations (approximately)
Xr = reconstruct(M, Yte)

# group results by testing set labels for color coding
setosa = Yte[:,Xte_labels.=="setosa"]
versicolor = Yte[:,Xte_labels.=="versicolor"]
virginica = Yte[:,Xte_labels.=="virginica"]
```

```
size(Xte)
>>(4, 75)
size(Yte)
>>(3, 75)
```

把降维后的数据画出来
```
using Plots
p = scatter(setosa[1,:],setosa[2,:],setosa[3,:],marker=:circle,linewidth=0)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%9713.png)

```
scatter!(versicolor[1,:],versicolor[2,:],versicolor[3,:],marker=:circle,linewidth=0)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%9714.png)

```
scatter!(virginica[1,:],virginica[2,:],virginica[3,:],marker=:circle,linewidth=0)
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%9715.png)



