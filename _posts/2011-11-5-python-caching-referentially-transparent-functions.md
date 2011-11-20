---
layout: post
title: Caching Referentially Transparent Functions (Python)
---

{{ page.title }}
================

Because such a function will always produce the same output given a same input, it is quite easy to cache.

*Note: this is an illustration, with shortcomings. For example: calling a function with named arguments in different orders will cause a cache miss, although this is still an equivalent call.*

{% highlight python %}

cache = {}

def cached(func):
    def wrapper(*args, **kwargs):
        global cache
        try:
            return cache[str((args, kwargs))]
        except KeyError:
            res = func(*args, **kwargs)
            cache[str((args, kwargs))] = res
            return res
    return wrapper

@cached
def fibonacci(n):
    if n == 0: return 0
    if n == 1: return 1
    else: return fibonacci(n-1) + fibonacci(n-2)

@cached
def make_full_name(first_name, last_name):
    return ("%s %s" % (first_name, last_name))
{% endhighlight %}