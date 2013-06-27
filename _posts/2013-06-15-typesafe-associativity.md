---
layout: post
title: Type-safe associativity
published: false
---

I recently watched a [talk by Guy Steele] [1] discussing the impact various algebraic properties have on automated code optimization.  After a _very_ long introduction, which I found to be largely unneccessary for the main message of the presentation, Guy brings up his main points, which boil down to:

1. The best way to write parallel applications is not to have to think about parallelism
2. Accumulators are BAD for automated parallelism, Divide-and-conquer is GOOD
3. Associativity allows the reduce portion (where the "divided" results are merged) to be optionally executed in parallel
    * Other algebraic properties also allow some nice optimizations (more on this in a later post)
4. Programmers need help validating/ensuring these properties, so they don't mistakenly tell the compiler to parallelize some operation

This got me thinking about how to encode these properties to provide some compile-time safety, ideally as part of the type system?

# The Groundwork

First, we need the basic building blocks.  A binary operator is a function which takes two arguments of the same type and returns a value of that type.

{% highlight scala linenos %}
/* 
   Note that a true implementation of this would use view-bounds to
   allow existing functions to act as BinaryOperations if they 
   satisfy the type constraints
*/
   trait BinaryOperation[T]() extends Function2[T, T, T] {
    def apply(v1: T, v2: T): T
}
{% endhighlight %}

Most of the commonly used operation in math are _associative_.  Recall that an operation, `*`, is associative if:

> a*(b*c)=(a*b)*c

What we'd like to do is to create a trait that can be added to an implementation of `BinaryOperation` that defines what it means to be associative.  We quickly run into a problem though; an associative binary operator looks exactly like a non-associative binary operator, the only difference is an additional guarantee that grouping does not effect the result.  This is a _behavioral_ distinction, but one that we would like to encode within the type system.

FINISH THIS

[1]: http://www.infoq.com/presentations/Thinking-Parallel-Programming
