---
layout: post
title: Functional Purity (the dangers of non-referentially transparent functions)
---

{{ page.title }}
================

People have asked me what is so attractive about a purely functional language like [Haskell](http://haskell.org/). Well I thought that instead of tyring to convince people with words I would simply give an example.

*Note: Now it is clear that even Haskell must deal with impure code if it wants to have any effect on the world (print something to your terminal, talk to a server or read a file), but Haskell programs tend to keep impure functions to a minimum, by lifting pure code into the IO monad at the edges of the program.*

Here is a very simple piece of C code that shows the dangers of impure code:

{% highlight c %}
#include <string.h>
#include <stdio.h>
#include <stdlib.h>

struct person{
    char *name;
    int age;
};
int get_age(struct person *p) {
    /* danger */
    p->name = "Nicholas";
    return p->age;
}
int main() {
    struct person *oliver; 
    oliver = malloc(sizeof(struct person));
    oliver->name = "Oliver";
    oliver->age  = 23;
    get_age(oliver);
    // What is the value of oliver->name now?
    // You cannot guarantee it!
    free(oliver);
    return 0;
}
{% endhighlight %}

So what's the problem? The type of the `get_age` function is that it takes a pointer to a `struct person` and returns an `int`. Given the name of the function and it's type you would assume that it act like a simple getter for the age attribute of the struct. Yet there is NO GUARANTEE of this! Because of an error on my part the `get_age` function changes the person's name. If I didn't know this as a user of this function I may very well produce buggy code. Contrast this to the following haskell code:

{% highlight haskell %}

data Person = Person { _name :: String
                       _age  :: Int }

getAge :: Person -> Int
getAge = _age

{% endhighlight %}

The `getAge` function's type signature is `getAge :: Person -> Int` which means that it takes a `Person` and returns an `Int`. Because of Haskell's incredibly strict type system I CAN GUARANTEE that it will not change the person's name by mistake (or on purpose). Haskell's purity guarantees that a function that is not in the IO Monad will be referentially transparent.

*Note: This article is not entirely correct. I am making some terrible simplifications here. Firstly one can for example argue that even actions in the IO monad are pure. Secondly there are ways of overcoming Haskell's type restrictions namely `unsafePerformIO`. Nonetheless this frame of mind, when it comes to pure vs. inpure code suits most situations (my opinion).*
