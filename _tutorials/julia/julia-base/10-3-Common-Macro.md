---
youtube_id: 6DBUHOjOmKo
qq_video_id: r0833jjrny2
b_av: 47775635
b_cid: 83684981
b_page: 26
title: Julia中常用的宏
description: "Julia中常用的宏"
chapter: 10
category: julia-base
post-headings:
author-link: #
no-video: false
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/julia-base.png
index: 26
---

### 常用的宏

 - @time
 - @show
 - @which

@time
```
@time 1 + 2
@time println("Hello world!")
@time sleep(1)
```

@time就是用下面的方法实现的。
```Julia
macro tid(expre)
    quote
        local t0 = time()
        local val = $expre
        elapsedtime = time() - t0
        println("$elapsedtime seconds")
        val
    end
end

@tid map(x->x^2, 1:10000)
```

@which
```
@which 1+2
@which sleep(2)
```

@show
```
x = rand(10)
@show sum(x)
@show cumsum(x)
```


