---
youtube_id: D_PU33GA8b0
qq_video_id: e0830aj1p6j
b_av: 47775635
b_cid: 83681918
b_page: 7
title: Julia字符串
description: "字符串！！又见蛋疼的编码"
chapter: 2
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 7
---

## 字符串
```Julia
x = 'a' #用单引号表示字符
Int(x)
Char(ans)
## ASCII码对应
'\u21' # !
Int('\t')

str = "Hello World!"
str[1]  #Julia的下标从1开始
str[end-3:end]
```


**又见蛋疼的编码**


Julia 完整支持 Unicode 字符和字符串,Unicode码位可以使用`\u`和`\U`来转义，在Julia中，非ASCII字符使用UTF-8编码，但UTF-8编码是一种变长的编码方式，也就是说不同字符的编码长度可能不同，这就导致在使用一些非常见字符时可能会碰到蛋疼的问题。
```Julia
str = "\u2200 x \u2203 y"
>>"∀ x ∃ y"
str[1]
>>'∀': Unicode U+2200 (category Sm: Symbol, math)
str[2]
>>ERROR:...
str[3]
>>ERROR:...
str[4]
>>' ': ASCII/Unicode U+0020 (category Zs: Separator, space)
```
在UTF-8的编码中，'\u2200'即'∀'使用了三个字符，因此str[2]和str[3]都是无效的。str[4]为空格。
但可以这么使用：
```Julia
str[1:4]
```
也可以使用`firstindex()`和`lastindex()`来索引
```Julia
for i = firstindex(str):lastindex(str)
    try
        println(s[i])
    catch
    
    end
end
>>∀
 
x
 
∃
 
y
```
当然，也可以像Python一样，直接将字符串当做遍历对象，而且不需要异常处理：
```Julia
for i in s
    println(i)
end
```








**字符串的其他操作**
```Julia
x = "Helllo"
y = "World"
string(x,",",y,"\n")
>>Hello,World\n
x * ',' * y
>>"Hello,World"
string("$x $y !")
>>"Hello World !"
n = 4
"nnnn has $n n"
>>"nnnn has 4 n"
"1/3 is about $(1/3)"
>>"1/3 is about 0.3333333333333333"
"$x,$y\n"
>>"Helllo,World\n"
"1 + 2 = $(1 + 2)"
>>"1 + 2 = 3"
v = [1,2,3]
"v: $v"
>>"v: [1, 2, 3]"
# 由于$是一个特殊字符，在当做文字使用时需转义
println("\$100")
>>$100
"abc" ^ 3
>>"abcabcabc"
lowercase("HELLO")
uppercase("hello")
replace("I want to learn Python", "Python" =>  "Julia")
replace("ABCD", 'A'=>'E')
startswith("julia is interesting", "julia")
>>true
startswith("  julia is interesting", "julia")
>>false
startswith(strip("  julia is interesting"), "julia")
>>true
s = split("one two three")
>>3-element Array{SubString{String},1}:
 "one"  
 "two"  
 "three"
join(s)
>>"onetwothree"
join(s, " ")
>>"one two three"
```
跨多行的字符串
```Julia
"""
Hello,
world."""
>>"Hello,\nworld."
```
字符串的数学运算
```Julia
“abcde" < "xyz"
>>true
"abcd" != "abcde"
>>true
"1 + 2 = 3" == "1 + 2 = $(1+2)"
>>true
findfirst(isequal('x'),"xilinx")
>>1
findnext(isequal('x'),"xilinx",1)
>>1
findnext(isequal('x'),"xilinx",2)
>>6
occursin("world", "Hello, world.")
>>true
repeat('a',10)
>>"aaaaaaaaaa"
x = "abcd..."
repeat(x,10)
>>"abcd...abcd...abcd...abcd...abcd...abcd...abcd...abcd...abcd...abcd..."
join(["abcd","efg","kmn","xyz"],","," and ")
>>"abcd,efg,kmn and xyz"
x = "abcdefgh"
firstindex(x)
>>1
lastindex(x)
>>8
length(x)
>>8
length(x,2,4)
```
有字符串的地方当然少不了正则表达式
```Julia
r"^\s*(?:#|$)"
typeof(ans)
>>Regetx
occursin(r"^\s*(?:#|$)", "not a comment")
>>false
occursin(r"^\s*(?:#|$)", "# a comment")
>>true
m = match(r"abc","abcdefgh")
>>RegexMatch("abc")
m = match(r"[0-9]","aaaa1aaaa2aaaa3",1)
>>RegexMatch("1")
m = match(r"[0-9]","aaaa1aaaa2aaaa3",6)
>>RegexMatch("2")
```

