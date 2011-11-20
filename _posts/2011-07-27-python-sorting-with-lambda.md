---
layout: post
title: Sorting With Lambda (Python)
---

{{ page.title }}
================

Today somebody on #python on freenode asked about some examples of when they would need lambdas in python. Here is a simple example of how to do a non-trivial sort:

{% highlight python %}

>>> class obj(object):
...     def __init__(self):
...             self.data = []
...     def add(self, d):
...             self.data.append(d)
...
>>> a = obj()
>>> a.add(1)
>>> a.add(2)
>>> b = obj()
>>> b.add(1)
>>> b.add(2)
>>> b.add(3)
>>> c = obj()
>>> objs = [a,b,c]
>>> objs.sort(key=lambda obj: len(obj.data))
>>> objs[0].data
[]
>>> objs[1].data
[1, 2]
>>> objs[2].data
[1, 2, 3]

{% endhighlight %}