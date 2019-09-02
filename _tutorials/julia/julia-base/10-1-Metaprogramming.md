---
youtube_id: C7pLgXYNVFI
qq_video_id: f0833ch8s80
b_av: 61523729
b_cid: 106541162
b_page: 1
title: Julia元编程
description: "什么是元编程？Julia中怎么使用元编程？"
chapter: 10
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 24
---
 

# 元编程



**什么是元编程**



维基百科上的解释为：

元编程（英语：Metaprogramming），又译超编程，是指某类计算机程序的编写，这类计算机程序编写或者操纵其它程序（或者自身）作为它们的资料，或者在运行时完成部分本应在编译时完成的工作。多数情况下，与手工编写全部代码相比，程序员可以获得更高的工作效率，或者给与程序更大的灵活度去处理新的情形而无需重新编译。

是不是没看懂？没关系，因为我刚开始也没看懂。知乎上有一个关于元编程的解释是比较直观的。

[知乎原回答](https://www.zhihu.com/question/23856985)

Meta- 这个前缀在希腊语中的本意是「在…后，越过…的」，类似于拉丁语的 post-，比如 metaphysics 就是「在物理学之后」，这个词最开始指一些亚里士多德的著作，因为它们通常排序在《物理学》之后。但西方哲学界在几千年中渐渐赋予该词缀一种全新的意义：关于某事自身的某事。比如 meta-knowledge 就是「关于知识本身的知识」，meta-data 就是「关于数据的数据」，meta-language 就是「关于语言的语言」，而 meta-programming 也是由此而来，是「关于编程的编程」。弄清了词源和字面意思，可知大陆将 meta- 这个前缀译为「元」并不恰当。台湾译为「后设」，稍微好一点点，但仍旧无法望文生义。也许「自相关」是个不错的选择，「自相关数据」、「自相关语言」、「自相关编程」——但是好像又太罗嗦了。Anyway。先看看 meta-data：「我的电话是 +86 123 4567 8910」  ——这是一条数据；「+86 123 4567 8910 有十三个数字和一个字符，前两位是国家代码，后面是一个移动电话号码」   —— 这是关于前面那条数据的数据。

那么照猫画虎，怎样才算 meta-programming 呢？泛泛来说，只要是与编程相关的编程就算是 meta-programming 了——比如，若编程甲可以输出 A - Z，那么写程序甲算「编程」；而程序乙可以生成程序甲（也许还会连带着运行它输出 A - Z），那么编写程序乙的活动，就可以算作 meta-programming，「元编程」。

那我们看看Julia中的元编程到底是什么及如何应用？


## 程序表示

每个Julia程序都是以字符串开始的
```Julia
str1 = "1 + 1"
>>"1 + 1"
# 把字符串解析成表达式
ex1 = Meta.parse(str1)
>>:(1 + 1)
typeof(ex1)
>>Expr
# 我们也可以直接定义成parse的形式
ex2 = :(1 + 1)
typeof(ex2)
>>Expr
```
Expr对象包含两个部分，一个标识表达式类型的Symbol，一个是表达式的参数
```Julia
fieldnames(typeof(ex1))
>>(:head, :args)
ex1.head
>>:call
ex1.args
>>3-element Array{Any,1}:
  :+
 1
 1
Meta.show_sexpr(ex1)
>>(:call, :+, 1, 1)
```
所以我们也可以用 prefix notation来构造
```Julia
ex3 = Expr(:call, :+, 1, 1)
:(1 + 1)
```
当然我们也可以求出表达式的结果
```Julia
eval(ex3)
>>2
```
dump函数可以显示Expr对象
```Julia
dump(ex1)
>>Expr
  head: Symbol call
  args: Array{Any}((3,))
    1: Symbol +
    2: Int64 1
    3: Int64 1
```

### Symbol

构造一个Symbol
```Julia
:foo
>>:foo
typeof(ans)
>>Symbol
:foo == Symbol("foo")
>>true
```
Symbol中如果是多个参数，则表示将这些参数连起来
```Julia
Symbol("foo",123)
>>:foo123
```
### Quote

我们前面定义的表达式都是数值表达式，元编程也允许字符表达式
```Julia
ex4 = :(a+b*c+1)
>>:(a + b * c + 1)
typeof(ex4)
>> Expr
```
如果是多行表达式，可以在`quote...end`中实现
```Julia
ex4 = quote
    x = 1
    y = 2
    x + y
end
typeof(ex4)
>>Expr
```
可以使用$符号，把数值代入到符号表达式中
```Julia
a = 1
ex = :($a + b)
>>:(1 + b)
```
如果是array的表达式，则要采用如下形式
```Julia
args = [:x, :y, :z]
:(f(1, $(args...)))
>>:(f(1,x,y,z))
```
### 嵌套Quote
```Julia
x = :(1 + 2)
>>e = quote quote $x end end
```
此时我们用`eval`查看此表达式的结果
```Julia
eval(e)
>>quote
    #= none:1 =#
    1 + 2
end
```
也就是说，该表达式的内容仍然是一个quote；那我们怎么取出最里层的内容呢？
```Julia
e = quote quote $$x end end
eval(e)
>>quote
    #= none:1 =#
    3
end
```

### 函数中的表达式
```Julia
function math_expr(op, op1, op2)
    expr = Expr(:call, op, op1, op2)
    return expr
end
ex = math_expr(:+, 1, Expr(:call, :*, 4, 5))
>>:(1 + 4 * 5)
eval(ex)
>>21
```

```
function make_expr2(op, opr1, opr2)
   opr1f, opr2f = map(x -> isa(x, Number) ? 2*x : x, (opr1, opr2))
   retexpr = Expr(:call, op, opr1f, opr2f)
   return retexpr
end
make_expr2(:+, 1, 2)
>>:(2 + 4)
ex = make_expr2(:+, 1, Expr(:call, :*, 5, 8))
>>:(2 + 5 * 8)
eval(ex)
>>42
```

## Macros
Macro也是Julia元编程的一个重要应用，Macro是一种规则，或称为语法替换，这种替换在预编译时进行，Macro在运行时没有像函数似的调用时间。
```Julia
macro HelloWorld()
    return :( println("Hello World!")
end
```
编译器会把所有的@HelloWorld换成`:( println("Hello World!")`
```Julia
@HelloWorld()
>>Hello World!
```
Macro也可以带参数
```Julia
macro ILike(str)
    return :( println("I like ", str))
end
@ILike("Julia")
>>I like Julia
```
我们可以用`macroexpand`看到返回的quote 表达式，这也是宏调试时一个非常重要的工具
```Julia
ex = macroexpand(Main, :(@ILike("Julia")))
>>:((Main.println)("I like ", "Julia"))
typeof(ex)
>>Expr
```
也可以用`@macroexpand`查看返回的quote表达式
```Julia
@macroexpand @ILike "Julia"
>>:((println)("I like ", "Julia"))
```
`@macroexpand`还有一个重要用途，我们看下面的例子
```Julia
macro testExpend(arg)
    println("I execute at parse time. The argument is: ", arg)
    return :(println("I execute at runtime. The argument is: ", $arg))
end
```
两个println语句，一个是julia语句，一个表达式，如果我们直接运行`@testExpand((1,2))`
```Julia
@testExpand((1,2,3))
>>I execute at parse time. The argument is: (1, 2, 3)
I execute at runtime. The argument is: (1, 2, 3)
```
两个println语句都有输出；如果我们定义一个表达式
```Julia
ex = macroExpand(Main, :(@testExpand :(1,2,3)))
>>I execute at parse time. The argument is: $(Expr(:quote, :((1, 2, 3))))
```
可以看出，当表达式被定义时，第一个println的内容就打印出来了
```Julia
eval(ex)
>>I execute at runtime. The argument is: (1, 2, 3)
```
而第二个println函数则在表达式被运行时才执行




</font>


