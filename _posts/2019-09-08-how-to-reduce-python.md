---
layout: post
title: "Use Python's reduce like a pro"
date: 2019-09-08
categories: Python FP functional
---

## Intended Audience
This article is for two types of readers:
1.  You're tired of Python's training wheels.  You want to use 
    Functional Programming.  You understand the tradeoffs.  You want 
    to use `reduce` like a pro.  
2.  You need to read code by people who fancy themselves in the former
    category and so you need to understand how `reduce` works.
    
## Overview
This article is about how to read and write code using `reduce`.  

This article will not directly cover:
* [How `reduce` works]({% post_url 2019-09-09-how-reduce-works %})
* Why and when to use `reduce`
* Good style for writing `reduce`
* Advanced patterns using `reduce`

These topics will be covered in later articles.

## Anatomy of **reduce**

There are three parts to `reduce`.

1. The **fold** `function` -- a binary[^2] function
2. The element `sequence` -- an iterable
3. The `initial` data structure

[^2]: Fancy way of saying a function that takes two arguments.

## Writing **fold** functions

The key to writing `reduce` code is understanding the relationship
between the arguments of the `fold` function and the `initial` 
data structure.

## **fold** examples
The `fold` function works like this:

{% highlight python %}
# pseudo code
def fold_something(a: SomeType, b: SomeOtherType) -> SomeType:
    ...

# usage
>>> reduce(fold_something, some_data, SomeType())
{% endhighlight %}

In the following examples, observe that the `a` argument to the **fold** 
function -- called `fold_something` in the previous example --, the `return` 
of the **fold** function, and the `initial`
data structure all have the same "spec".

{% highlight python %}
from functools import reduce

def add(a: (int or float), b) -> (int or float):
    return a + b    
# add usage with reduce
>>> reduce(add, range(10), 0)
45

def cons(a: list, b) -> list:
    return [b, *a]
# cons usage with reduce
>>> reduce(cons, {'a': 1, 'b': 2}, [])
['b', 'a']
    
def conj(a: list, b) -> list:
    return [*a, b]
# conj usage with reduce
>>> reduce(cons, {'a': 1, 'b': 2}, [])
['a', 'b']


def increment_occurences(d: dict, x) -> dict:
    return {**d, x: d[x] + 1} if x in d else {**d, x: 1}
    
>>> reduce(increment_occurences, 'bookkeeper', {})
{'b': 1, 'e': 3, 'k': 2, 'o': 2, 'p': 1, 'r': 1}

{% endhighlight %}

(click 
<a href="https://trinket.io/python3/2d79cc4414" target="_blank">here</a> 
to see these examples in action -- opens a new tab)

### The "spec" of `reduce` arguments

Stay with me here. This isn't about types, _per se_ -- it's about the _spec_. 
The point is, the `initial` argument, the `a` argument of `fold`, and the `return`
value of `fold` all need to be the same _spec_:

* if `a` is an `int` or `float`, `fold` needs to `return` an `int` or 
`float`  and the `initial` value needs to be an `int` or `float`. 

* if `a` is a `dict` with keys `'name'` and `'age'`, `fold` needs 
to `return` a `dict` with keys `'name'` and `'age'`, and the `initial`
value needs to be a `dict` with keys `'name'` and `'age'`.

* if `a` is a `str`, `fold` needs to `return` a `str`, and the
`initial` value needs to be a `str`. 

* if `a` is a `list`, `fold` needs to `return` a `list`, and the
`initial` value needs to be a `list`.  

## **reduce** example that can't be duplicated by **map**.

Here, we use `reduce` to perform a _cumulative sum_[^1] sum operation.

[^1]: [cumulative sum definition](http://mathworld.wolfram.com/CumulativeSum.html)!

This example is a good use case for `reduce` since it can not be 
accomplished with any combination of `map` and `filter` or 
`list comprehensions`.

{% highlight python %}

from functools import reduce
# cumulative sum 

# fold function
def next_csum(xs: list, x) -> list:
  # note that `xs` and the `return` are both `list`
  if len(xs) == 0:
    return [x]
  else:
    return [*xs, xs[-1] + x]
    
if __name__ == '__main__':
  print(reduce(next_csum, 
               range(10), 
               [] # <--- note that the INITIAL value is ALSO a list
               ))
{% endhighlight %}

<iframe src="https://trinket.io/embed/python3/9fe83f87f0" width="100%" height="356" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>
Here we see `next_csum` as the **fold** function. As pointed out in the
code comments, `xs`, the `return` of `next_csum`, and the third argument 
(`initial`) or the `reduce` function on line 13 are all a _list_.

Here you can see the operation occuring explicitly:
<iframe width="100%" height="600" frameborder="0" src="https://pythontutor.com/iframe-embed.html#code=from%20functools%20import%20reduce%0A%23%20cumulative%20sum%0A%0A%23%20fold%20function%0Adef%20next_csum%28xs%3A%20list,%20x%29%20-%3E%20list%3A%0A%20%20%23%20note%20that%20%60xs%60%20and%20the%20%60return%60%20are%20both%20%60list%60%0A%20%20if%20len%28xs%29%20%3D%3D%200%3A%0A%20%20%20%20return%20%5Bx%5D%0A%20%20else%3A%0A%20%20%20%20return%20%5B*xs,%20xs%5B-1%5D%20%2B%20x%5D%0A%20%20%20%20%0Aif%20__name__%20%3D%3D%20'__main__'%3A%0A%20%20sequence%20%3D%20range%2810%29%0A%20%20initial%20%3D%20%5B%5D%0A%20%20print%28reduce%28next_csum,%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20sequence,%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20initial%20%23%20%3C---%20note%20that%20the%20INITIAL%20value%20is%20ALSO%20a%20list%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29%29%0A&codeDivHeight=400&codeDivWidth=350&cumulative=false&curInstr=15&heapPrimitives=nevernest&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false"> </iframe>

In future articles we'll cover:

* How `reduce` works
* Why and when to use `reduce`
* Good style for writing `reduce`
* Advanced patterns using `reduce`





