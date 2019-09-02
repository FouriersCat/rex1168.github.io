---
youtube_id: OpxExN_Ldk0
qq_video_id: k083331qiqj
b_av: 61523729
b_cid: 106654483
b_page: 12
title: Julia机器学习之决策树
description: "Julia机器学习之决策树"
chapter: 12
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 35
---

### 决策树

使用iris数据集
```
using RDatasets
iris = dataset("datasets", "iris")
features = convert(Array, iris[:, 1:4])
labels = convert(Array, iris[:, 5])
```

调用决策树
```
using DecisionTree
```
在该库中，包含了

 - Decision Tree Classifier
 - Random Forest Classifier
 - Adaptive-Boosted Decision Stumps Classifier
 - Regression Tree
 - Regression Random Forest
 
我们这里只介绍分类模型，即前三种算法模型

```
model = build_tree(labels, features)
>>Decision Tree
Leaves: 9
Depth:  5
```

```
model = prune_tree(model, 0.9)
>>Decision Tree
Leaves: 8
Depth:  5
```

```
print_tree(model, 5)
>>Feature 3, Threshold 2.45
L-> setosa : 50/50
R-> Feature 4, Threshold 1.75
    L-> Feature 3, Threshold 4.95
        L-> versicolor : 47/48
        R-> Feature 4, Threshold 1.55
            L-> virginica : 3/3
            R-> Feature 3, Threshold 5.449999999999999
                L-> versicolor : 2/2
                R-> virginica : 1/1
    R-> Feature 3, Threshold 4.85
        L-> Feature 2, Threshold 3.1
            L-> virginica : 2/2
            R-> versicolor : 1/1
        R-> virginica : 43/43
```

```
print_tree(model, 3)
>>Feature 3, Threshold 2.45
L-> setosa : 50/50
R-> Feature 4, Threshold 1.75
    L-> Feature 3, Threshold 4.95
        L-> versicolor : 47/48
        R-> 
    R-> Feature 3, Threshold 4.85
        L-> 
        R-> virginica : 43/43

```

按照下面的方式

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%9712.png)


用决策树模型做判断
```
apply_tree(model, [5.9, 3.0, 5.1, 1.9])
>>"virginica"
apply_tree(model, [1.9, 2.0, 2.1, 1.9])
>>"setosa"
```

nfold cross validation for pruned tree
```
n_folds=3
accuracy = nfoldCV_tree(labels, features, n_folds, 0.9)
>>3×3 Array{Int64,2}:
 24   0   0
  0  13   0
  0   0  13
3×3 Array{Int64,2}:
 12   0   0
  0  17   0
  0   0  21
3×3 Array{Int64,2}:
 14   0   0
  0  20   0
  0   1  15

Fold 1
Classes:  ["setosa", "versicolor", "virginica"]
Matrix:   
Accuracy: 1.0
Kappa:    1.0

Fold 2
Classes:  ["setosa", "versicolor", "virginica"]
Matrix:   
Accuracy: 1.0
Kappa:    1.0

Fold 3
Classes:  ["setosa", "versicolor", "virginica"]
Matrix:   
Accuracy: 0.98
Kappa:    0.9695863746958637

Mean Accuracy: 0.9933333333333333
3-element Array{Float64,1}:
 1.0 
 1.0 
 0.98
```

### adaptive boosting

构造adaboost_stumps模型
```
model, coeffs = build_adaboost_stumps(labels, features, 10)
>>(Ensemble of Decision Trees
Trees:      10
Avg Leaves: 2.0
Avg Depth:  1.0, [0.346574, 0.255413, 0.264577, 0.335749, 0.288846, 0.258287, 0.356882, 0.298766, 0.242485, 0.349976])
```

应用模型判断
```
apply_adaboost_stumps(model, coeffs, [5.9, 3.0, 5.1, 1.9])
>>"virginica"
```

adaboost nfold cross-validation
```
# 3 folds, 8 iteration
accuracy = nfoldCV_stumps(labels, features, 3, 8)
```


### 随机森林

构造随机森林模型
```
# 2 random features, 10 trees, 0.5 portion of samples
model = build_forest(labels, features, 2, 10, 0.5)
>>Ensemble of Decision Trees
Trees:      10
Avg Leaves: 6.4
Avg Depth:  4.4
```

应用模型判断
```
apply_forest(model, [5.9, 3.0, 5.1, 1.9])
>>"virginica"
```

forest  nfold cross-validation

```
accuracy = nfoldCV_forest(labels, features, 3, 2)
>>3×3 Array{Int64,2}:
 12   0   0
  0  15   0
  0   1  22
3×3 Array{Int64,2}:
 18   0   0
  0  13   2
  0   1  16
3×3 Array{Int64,2}:
 20   0   0
  0  20   0
  0   0  10

Fold 1
Classes:  ["setosa", "versicolor", "virginica"]
Matrix:   
Accuracy: 0.98
Kappa:    0.9689440993788819

Fold 2
Classes:  ["setosa", "versicolor", "virginica"]
Matrix:   
Accuracy: 0.94
Kappa:    0.9096385542168673

Fold 3
Classes:  ["setosa", "versicolor", "virginica"]
Matrix:   
Accuracy: 1.0
Kappa:    1.0

Mean Accuracy: 0.9733333333333333
3-element Array{Float64,1}:
 0.98
 0.94
 1.0 
```


