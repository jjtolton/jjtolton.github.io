---
layout: post
title: "How Python's reduce works"
date: 2019-09-09
categories: Python FP functional reduce
---

## Intended Audience 

You've have a basic idea of how `map` and `filter` work, but `reduce`
remains elusive and you want to learn more about it.
You might even be flirting with the forbidden path 
of Functional Programming. 

## What this article covers

How `reduce` works

## What this article doesn't cover

* [How to write `reduce` code]({% post_url 2019-09-08-how-to-reduce-python %})
* When and where to use `reduce` (and when not to!)
* Good `reduce` style
* Advanced `reduce` patterns

These topics will be covered in other articles and linked here
as they become available.

## What is this **reduce** thing?

If you can read the following code, this will explain it concisely 
(and if you can't, don't worry -- we'll go over it).

{% highlight python %}

# NOTE: this is for demonstrative purposes only,
#   ..: not the actual implementation
def reduce(fold, sequence, initial):
    res = initial
    for x in sequence:
        res = fold(res, x)
    return res
{% endhighlight %}

This version[^1] of `reduce` takes three arguments:

[^1]: The actual implementation of `reduce` takes two or three arguments.
    The two argument form uses the first value of the **sequence** as the 
    **initial** value.
!
1. The **fold** function
2. The **sequence**
3. The **initial** data

### The **fold**

The **fold** is a function that takes two arguments and returns 
a value.  It is absolutely critical that the output of the **fold**
be the same kind of thing as the first argument to the **fold**.

Remember this snippet from 5 seconds ago?
{% highlight python %}
    for x in sequence:
        res = fold(res, x)
{% endhighlight %}
This is where all the work of `reduce` gets done. We take the output
of the **fold** and put it right back into itself -- with extra
added ingredient `x`.  This creates a new `res` -- and the cycle 
continues until the `sequence` is complete.

Here's a diagram of this `for`-loop if you're more visually inclined:

![visualization-of-reduce](/assets/img/reduceFold.svg)


So you can bet if the output of the **fold** is different than the
input, something will break!

That's all I'm going to say about the **fold** for now.
I cover how to write a good **fold** extensively [here](https://hiimjayhireme.github.io/python/fp/functional/2019/09/08/how-to-reduce-python.html).

### The **sequence**

The **sequence** can be anything you can use in a `for` loop -- 
so this can be a `list`, `tuple`, `set`, `string`, `dict`[^2],
or any class or object that implements the `__iter__` method.  

### The **initial** value

This is the first argument to the **fold** function on the first
iteration.  In Python's implementation of `reduce`, the 
initial function is optional. If you don't provide `initial`, 
the function works like this:


{% highlight python %}
def reduce(fold, *args):
    if len(args) == 2:
        sequence, initial = args
    else:
        (initial, *sequence), *_ = args
    
    res = initial
    for x in sequence:
        res = fold(res, x)
    return res
{% endhighlight %}

(if `(initial, *sequence), *_ = args` looks confusing, check out
my article on WYSIWYG programming!)[^3]

Here's a diagram that shows the flow when there is no initial argument:

![reduce-startup-when-no-initial-argument](/assets/img/reduceNoInitial.svg)

Here's a diagram that shows reduce when an initial argument is given:

![reduce-start-with-initial-argument-provided](/assets/img/reduceInitial.svg)

## The key to **reduce**

Hopefully it's obvious now that `reduce` is fairly simple.
Conceptually, it's just a `for`-loop that repeatedly performs
`res = fold(res, x)` for `x` in a **sequence**. The real
art of `reduce` comes from understanding the **fold**
function. 

#### Further reading:

* [How to write a `fold` function]({% post_url 2019-09-08-how-to-reduce-python %})



<hr>
[^2]: If you use a `dict` and you want to do keys AND values,
    use the `.items()` method!

[^3]: I'll post it here when I write it.




