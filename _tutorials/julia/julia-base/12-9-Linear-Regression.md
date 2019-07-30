---
youtube_id: kIXXqbZ-r2A
qq_video_id: b0833h107a9
b_av: 47775635
b_cid: 83686543
b_page: 38
title: Julia机器学习之线性回归
description: "Julia机器学习之线性回归"
chapter: 12
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 38
---


### 线性回归

```
using GLM, GLMNet, DataFrames
```

**lm模型**
```
data = DataFrame(X=[1,2,3], Y=[2,4,7])
ols = lm(@formula(Y ~ X), data)
>>StatsModels.DataFrameRegressionModel{LinearModel{LmResp{Array{Float64,1}},DensePredChol{Float64,LinearAlgebra.Cholesky{Float64,Array{Float64,2}}}},Array{Float64,2}}

Formula: Y ~ 1 + X

Coefficients:
              Estimate Std.Error  t value Pr(>|t|)
(Intercept)  -0.666667   0.62361 -1.06904   0.4788
X                  2.5  0.288675  8.66025   0.0732
```

线性回归模型标准差
```
stderror(ols)
>>2-element Array{Float64,1}:
 0.6236095644623245
 0.2886751345948133
```

应用模型
```
newX = DataFrame(X=[2,3,4]);
GLM.predict(ols, newX)
>>3-element Array{Union{Missing, Float64},1}:
 4.333333333333332
 6.833333333333335
 9.33333333333334 
```

```
data = DataFrame(X=[1,2,3], Y=[2,4,6])
ols = lm(@formula(Y ~ X), data)
newX = DataFrame(X=[2,3,4])
GLM.predict(ols, newX)
>>3-element Array{Union{Missing, Float64},1}:
 4.0
 6.0
 8.0
```


**glm模型**
```
data = DataFrame(X=[0,1,2,3,4], Y=[0.1296,0.0864,0.0576,0.0384,0.0256])
probit = glm(@formula(Y ~ X), data, Binomial(), ProbitLink())
>>StatsModels.DataFrameRegressionModel{GeneralizedLinearModel{GlmResp{Array{Float64,1},Binomial{Float64},ProbitLink},DensePredChol{Float64,LinearAlgebra.Cholesky{Float64,Array{Float64,2}}}},Array{Float64,2}}

Formula: Y ~ 1 + X

Coefficients:
              Estimate Std.Error   z value Pr(>|z|)
(Intercept)   -1.14184   1.32732 -0.860259   0.3896
X            -0.208282  0.659347 -0.315891   0.7521
```

```
newX = DataFrame(X=[1,2,3,4])
GLM.predict(probit, newX)
>>4-element Array{Union{Missing, Float64},1}:
 0.08848879660655808 
 0.05956904945792997 
 0.03864063839150974 
 0.024136051585243873
```


线性回归模型处理iris数据集

```
data = DataFrame();
data[:x] = iris[1:50, :SepalWidth]
data[:y] = iris[1:50, :SepalLength]
```

```
model = lm(@formula(y ~ x), data)
>>StatsModels.DataFrameRegressionModel{LinearModel{LmResp{Array{Float64,1}},DensePredChol{Float64,LinearAlgebra.Cholesky{Float64,Array{Float64,2}}}},Array{Float64,2}}

Formula: y ~ 1 + x

Coefficients:
             Estimate Std.Error t value Pr(>|t|)
(Intercept)     2.639  0.310014 8.51251   <1e-10
x             0.69049 0.0898989 7.68074    <1e-9
```

把LM拟合后的曲线跟原始的曲线做对比
```
using Plots
plot(GLM.predict(model))
plot!(data[:y])
```

![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%9716.png)



用GLM处理iris数据集

```
model = glm(@formula(y~x), data, Normal(), IdentityLink())
>>StatsModels.DataFrameRegressionModel{GeneralizedLinearModel{GlmResp{Array{Float64,1},Normal{Float64},IdentityLink},DensePredChol{Float64,LinearAlgebra.Cholesky{Float64,Array{Float64,2}}}},Array{Float64,2}}

Formula: y ~ 1 + x

Coefficients:
             Estimate Std.Error z value Pr(>|z|)
(Intercept)     2.639  0.310014 8.51251   <1e-16
x             0.69049 0.0898989 7.68074   <1e-13
```

把GLM拟合后的曲线跟原始的曲线做对比
```
using Plots
plot(GLM.predict(model))
plot!(data[:y])
```
![image](https://raw.githubusercontent.com/Bounce00/pic/master/Julia%20course/Julia%E7%A7%91%E5%AD%A6%E8%AE%A1%E7%AE%9717.png)



```
# Estimates for coefficents
coef(model)
# Standard errors of coefficents
stderror(model)
# Covariance of coefficents
vcov(model)
# RSS
deviance(model)

```




