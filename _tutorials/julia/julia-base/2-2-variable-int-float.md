---
youtube_id: dtwUyIPNFYA
qq_video_id: w0830uddxrt
b_av: 61523729
b_cid: 106754367
b_page: 29
title: 变量 整型 浮点
description: "Julia中最常用的数据类型"
chapter: 2
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 5
---


# 变量
可以参考知乎的文章[《Python/Matlab/Julia基本语法比较》](https://zhuanlan.zhihu.com/p/44114678)

像其他的动态语言一样，可以无需声明直接赋值
```Julia
x = 10
x = "Hello world!"
x = 1.1
x = "这是Julia教程
```
变量名还可以是中文，当然不推荐这么做
```Julia
测试 = 10
测试+1
````
还可以输入`\`+符号名称的方式来输入更多的Unicode数学字符，如`\alpha`后按`tab`键就会出现`α`的字符。
**命名规范**
跟其他编程语言的命名规范基本相同，如：
 - 变量名尽量小写
 - 类型和模块名首字母大写，单词间使用驼峰式分隔
 - 在几个单词不易区分时才以`_`分隔，一般不鼓励使用下划线`_`
 - 函数名和宏名使用小写字母，不使用下划线
 - 修改参数的函数结尾使用`!`，这样的函数被称为mutating functions或in-place functions





## 整型和浮点
**整数类型**

Type   |Signed |Number of bits|Smallest value|Largest value
-------|-------|--------------|-------------|-------------|
Int8   |  √    |8             |-2^7         |2^7-1        |
UInt8  |       |8             |0            |2^8-1        |
Int16  |  √    |16            |-2^15        |2^15-1       |
UInt16 |       |16            |0            |2^16-1       |
Int32  |  √    |32            |-2^31        |2^31-1       |
UInt32 |       |32            |0            |2^32-1       |
Int64  |  √    |64            |-2^63        |2^63-1       |
UInt64 |       |64            |0            |2^64-1       |
Int128 |  √    |128           |-2^128       |2^127-1      |
UInt128|       |128           |0            |2^128-1      |
Bool   |  N/A  |8             |false(0)     |true(1)      |

```Julia
a = typoef(2)
>>Int64
typeof(a)
>>DataType
typeof(DataType)
>>DataType
b = supertype(a)
>>Signed
supertype(Signed)
>>Integer
supertype(Integer)
>>Real
supertype(Real)
>>Number
supertype(Number)
>>Any
supertype(Any)
>>Any
subtypes(Integer)
>>3-element Array{Any,1}:
 Bool    
 Signed  
 Unsigned
 sizeof(1)
 >>8
 sizeof(Int16(1))
 >>2
```









**浮点数**
Type   |Precision|Number of bits
-------|---------|-------------|
Float16|half     |16           |
Float32|single   |32           |
Float64|double   |64           |

在32位系统中，整数默认是Int32类型，浮点数默认是Float32类型；
在64位系统中，整数默认是Int64类型，浮点数默认是Float64类型。
```Julia
x = 1
typeof(x)
>>Int64
a = tpyeof(1.1)
>>Float64
typeof(a)
>>DataType
typeof(DataType)
>>DataType
sizeof(Float64)
>>8
sizeof(Int128)
>>16
sizeof(Bool)
>>1
sizeof(Signed)
>>argument is an abstract type; size is indeterminate
    
    Stacktrace:
     [1] sizeof(::Type) at .\essentials.jl:405
     [2] top-level scope at In[15]:1
```
可以使用`Sys.WORD_SIZE`命令查看系统是32位还是64位，
也可以直接输入`Int`或`UInt`看系统位数

JUlia中的很多语法和REPL的用法都跟matlab很像，比如上一次的结果用ans表示
```Julia
julia> x = 1
1
julia> ans + 1
2
```

**十六进制**

由于Julia的整数中定义了Int和UInt两种大类型，其中Int用十进制表示，UInt类型用十六进制表示，当然我们也可以以`0b`开头表示二进制，以`0o`开头表示八进制
```Julia
x = 10
UInt8(x)
>>0x0a
x = 0b1010
Int64(x)
>>10
```
二进制和十六进制也支持有符号数，直接在前面加`-`即可
```Julia
-0xa
>>0xf6
bitstring(2)
>>"0000000000000000000000000000000000000000000000000000000000000010"
bitstring(-2)
>>"1111111111111111111111111111111111111111111111111111111111111110"
```
查看某种进制类型的最大最小值：
```Julia
(typemin(Int32), typemax(Int32))
>>(-2147483648, 2147483647)
typemax(Int64)+1
>>-9223372036854775808
```
浮点数表示方法
```Julia
1e-3
>>0.001
typeof(ans)
>>Float64
1f-3
>>0.001f0
typeof(ans)
>>Float32
```
浮点的0.0和-0.0在表示方式中也是有点分别的
```Julia
0.0 = -0.0
>>true
bitstring(0.0)
>>"0000000000000000000000000000000000000000000000000000000000000000"
bitstring(-0.0)
>>"1000000000000000000000000000000000000000000000000000000000000000"
```








**精度**
```Julia
eps(Float32)
>>1.1920929f-7
eps(Float64)
>>2.220446049250313e-16
```
数值越大，精度也就越低,也就是说，浮点数在0附近最稠密，随着数值越来越大，数值越来越稀疏，数值间的距离呈指数增长。
```Julia
eps(1.0)
2.220446049250313e-16
eps(1000.)
1.1368683772161603e-13
```
**舍入模型**
```Julia
1.1+0.1
>>1.2000000000000002
```
**任意精度的运算**
```Julia
BigInt(typemax(Int64)) + 1
>>9223372036854775808
x=BigFloat(1.1+0.1)
>>1.20000000000000017763568394002504646778106689453125
```
BigFloat也是有默认的长度的，不过我们可以调节，但每次调节只能do模块里面的精度
```Julia
setrounding(BigFloat, RoundUp) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
>>1.100000000000000000000000000000000000000000000000000000000000000000000000000003
setrounding(BigFloat, RoundDown) do
1.            BigFloat(1) + parse(BigFloat, "0.1")
       end
>>1.099999999999999999999999999999999999999999999999999999999999999999999999999986
setprecision(40) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
>>1.1000000000004
```


一个简单的递归函数，函数的具体用法将在后面讲到
```Julia
function showTypeTree(T, level = 0)
    println("\t" ^ level, T)
    for t in subtypes(T)
        if t!= Any
            showTypeTree(t, level + 1)
        end
    end
end
>>showTypeTree (generic function with 2 methods)
showTypeTree(Real)
>>Real
	AbstractFloat
		BigFloat
		Float16
		Float32
		Float64
	AbstractIrrational
		Irrational
	Integer
		Bool
		Signed
			BigInt
			Int128
			Int16
			Int32
			Int64
			Int8
		Unsigned
			UInt128
			UInt16
			UInt32
			UInt64
			UInt8
	Rational
```



