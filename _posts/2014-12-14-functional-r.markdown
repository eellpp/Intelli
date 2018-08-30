---
layout: post
title: "Functional R"
date: 2014-12-14T00:00:00+08:00
tags: R analytics
---

### Apply functions

#### mapply
Use mapply to operate on each element of multiple vectors

### find say we have monthly sales for two years and we want to find monthwise delta
mapply(function(x,y) x - y, year2,year1 )
[1]  2  4  6  8 10

#### lapply
Use lapply to operate on each element of a list and return a list
lapply(mtcars$mpg,function(x) sqrt(x))

#### sapply 
Use sapply to operate on each element of a list and return a vector
sapply(mtcars$mpg,function(x) sqrt(x))


### Vectorization : Replace a nested for loop with apply
Consider a nested looping example. For each element in list1, compare each element in list2, check if value exists and return 0 or 1 based on value exists

{% highlight bash %}
# method 1
sapply(list1, function(x)  if(x %in% list2) 1 else 0 )
#method 2 
sapply(list1, function(x) sapply(list2,  FUN = function(y) { if (x == y) 1 else  0 } ))
{% endhighlight %}

Lambda Expression

{% highlight bash %}
#Instead of writing the FUN like this :
> mapply(function(x){ return (x + 1)},t)

#we can apply a lambda expression : 
>mapply(function(x) (x + 1),t)

#lambda expression can be called immediately as
> {function(x) x + 10}(c(1:5))
[1] 11 12 13 14 15

{% endhighlight %}

R useful functions

http://www.personality-project.org/r/r.commands.html


