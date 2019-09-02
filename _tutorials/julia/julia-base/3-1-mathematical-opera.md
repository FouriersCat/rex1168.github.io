---
youtube_id: EnueGYn3gNE
qq_video_id: f0831jwgmwh
b_av: 61523729
b_cid: 106811549
b_page: 33
title: Julia数学运算
description: "都说Julia有比Matlab更直观的数学表达式，到底有多直观呢？"
chapter: 3
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 9
---


# 数学运算
比Matlab更直观的数学表达方式
```JUlia
x = 10
2x
>>20
```
但这就导致了可能会出现语法的冲突
 - 十六进制整数文本表达式 0xff 可以被解析为数值文本 0 乘以变量 xff
 - 浮点数文本表达式 1e10 可以被解析为数值文本 1 乘以变量 e10 
 - 
因此，Julia中

 - 以 0x 开头的表达式，都被解析为十六进制文本
 - 以数字文本开头，后面跟着 e 或 E ，都被解析为浮点数文本

**运算方法**

常用的 + - x /就不多说了，跟其他语言基本完全一样。

这里说一下向量运算，跟MATLAB的操作完全相同，比如向量的点乘,就是说对向量的元素一一操作
```Julia
[1,2,3].*3
>>3-element Array{Int64,1}:
 3
 6
 9
 
```
比较运算，支持链式比较
```Julia
1 <= 2 <= 3 == 3 <=5 >4 >=2
>>true
```
**常用的数学函数**
```Julia
# 进位函数
round(x)        #四舍五入
floor(x)        #向下取整
ceil(x)         #向上取整
trunc(x)        #trunc是直接砍掉小数，在正数的时候trunc跟floor一样，负数时跟ceil一样
# 除法函数
div(x,y)        #取模
fld(x,y)        #取小于结果的最大整数
cld(x,y)        #取大于结果的最小整数
rem(x,y)        #取余
mod(x,y)        
mod1(x,y1)      #如果x是y的整数倍，则返回y，不会返回余数
mod2pi(x)       #对2pi取余
divrem(x,y)     #返回取模的值和取余的值
fldmod(x,y)     #返回取小于x的最大整数和取余的值
gcd(x,y...)     #最大公约数
lcm(x,y...)     #最小公倍数
# 符号函数
abs(x)          #求模
abs2(x)         #求平方
sign(x)         #取符号
signbit(x)      #正数返回false，负数返回true
copysign(x,y)   #返回x * sign(y)
flipsign(x,y)   #返回x * sign(y) * -1
# 开根号 log
sqrt(x)         #开根号
cbrt(x)         #开三次根
hypot(x,y)      #sqrt(x^2 + y^2)
exp(x)          #e^x
expm1(x)        #e^-x
ldexp(x,n)      #x^n
log(x)          #loge(x)
log(b,x)        #logb(x)
log2(x)         #log2(x)     
log10(x)        #log10(x)
log1p(x)        #loge(1+x)
# 三角函数
sin    cos    tan    cot    sec    csc
sinh   cosh   tanh   coth   sech   csch
asin   acos   atan   acot   asec   acsc
asinh  acosh  atanh  acoth  asech  acsch
sinc   cosc
```



