---
layout: post
title: Function Composition (Python)
---

{{ page.title }}
================

If you know basic mathematics function composition will seem like a very simple and obvious thing. You are applying one function and then applying that function's result to another function. 

*Note: for this to work the return value(s) of the inner function must match the input expected by the outer function.*

The ability to compose functions is used mostly in functional programming languages (it is the main idea of that big terrible idea called *Monads*).

So how do you compose functions in Python? Well I think there is actually a built-in compose function, but I thought I would build one myself.

Composing two functions:

{% highlight python %}
def comp2(f1,f2):
    return lambda *args, **kwargs: f1(f2(*args,**kwargs))
{% endhighlight %}

Composing many functions:

{% highlight python %}
def comp(*funcs):
    if len(funcs) in (0,1):
        raise ValueError('need at least two functions to compose')
    # reverse funcs
    funcs = funcs[::-1]

# list of composed functions (to avoid recursion problems)
composed = []

# add last function
composed.append(funcs[0])

# we cannot use a for func in funcs here because of scope
# problems (will recurse infinitely)
i = 1
while i <= len(funcs) - 1:
    composed.append(comp2(funcs[i],composed[-1]))
    i += 1

return composed[-1]
{% endhighlight %}
