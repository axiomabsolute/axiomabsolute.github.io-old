---
layout: post
title: Response - I Wish Ruby Had Interfaces
truncate: 80
---

This post is written in response to a [blog post](http://victorsavkin.com/post/44861723903/i-wish-ruby-had-interfaces) about the role of documentation and types in interface definition.

> The only thing we can tell for sure is that the function takes two arguments. We can guess the types. Maybe the first one is a string and the second one is a configuration hash/object. But it is just a guess and we might be wrong. We have no idea what options go into the settings object (neither their names nor their types) or what this function returns.

There is no way we can call this function without checking the source code or the documentation. Everyone who advocates checking the source code does not understand why we have functions and classes. They can be used without us knowing anything about their implementation.

This seems to imply something very dangerous to me; that satisfying the type checker is sufficient to use an object that implements a given interface. Satisfying the type checker is enough to get your program to compile and run, but it means absolutely nothing in terms of program correctness, after all, there's still the possibility of runtimes errors or, worse, an unexpected result that does not produce an error at all.

## Shall I compareTheeTo a Summer's Day?

Consider the following well known Java interface

{% highlight java linenos %}
interface Comparable {
 int compareTo(T);
}
{% endhighlight %}

The following class perfectly satisfies this interface in terms of the types required

{% highlight java linenos %}
class ReallyBadComparable implements Comparable<Object> {
    public int compareTo(Object o){return -234;}
}
{% endhighlight %}

But the result is absolute rubish. We can guess what the method compareTo is supposed to do. We can guess that there is some meaning to the type of the return value, that negative, zero, and positive values have some additional meaning, but as the author said, this is just a guess and we might be completely wrong. The addition of type information guarentees the program will run, but not that the result will have any meaning. Without further documentation, we have no way of knowing what this return type means.

There is an entire _implicit interface_ defined in the documentation that is not at all enforced by the type signature. For example, while not required, it is usually assumed that the compareTo method is consistent with equals, that is, that if `e1.compareTo(e2) == 0` then `e1.equals(e2)`. Further, it is assumed that if `e1.compareTo(e2)` is negative, then `e2.compareTo(e1)` will be positive, otherwise we get mathematically contradictory results like `( 2.compareTo(3) < 0 ) && ( 3.compareTo(2) < 0 )`. But this shows up nowhere in the type definitions.

You could argue that this interface is poorly designed and that the type signature could be more expressive; that returning an integer instead of something like an enum is misleading, but I offer two counter arguments to that. First, as a developer using a library, if you knew nothing about the implicit contract of Comparable, how would you know it's poorly designed? Regardless of what the type signature is, it provides no indication of the quality of the API's design. You'll have to look at the documentation, implementation, and/or tests to gain some gauge of the quality, or blindly trust the library's author.

Secondly, how would you redesign this interface? Refactoring `compareTo` to return an enum does distinguish the return type from a general integer, but we're still left to guess at what the enumeration means, since the only thing we have to distinguish between the integer 1 and the enum value `xGreaterThanY` is the name, which we've already established is based on a (potentially faulty) guess. Put another way, what distinguishes the following two interfaces?

{% highlight java linenos %}
interface CartesianCoordinate {
double x;
double y;
 }
 
interface GeoCoordinate {
  double latitude;
  double longitude;
}
{% endhighlight %}

Structurally, these interfaces are identical and interchangeable, they differ only in name. But GeoCoordinates are typically only defined between certain ranges (`[-180,180]` and `[-90,90]` for latitude and longitude respectively, I believe). We could try to codify these restrictions into the type system, creating a new type for deciamlDegree values, but again, the type signature for this will tell you nothing except that it's a double!

## Types! We need more types!

Okay, so how about this, we'll include these types of restrictions so that an interface's type signature completely defines all possible values of that interface, that way we know, JUST from the type signature what's going on. Easy (if you accept my made up syntax of `type name : restriction`)!

{% highlight java linenos %}
interface GeoCoordinate {
 double latitude : -180 <= latitude <= 180;
 double longitude : -90 <= longitude <= 90;
}
{% endhighlight %}

Sweet! See how easy that is. Now let's write an interface to represent a URL. Great! We can use regular expressions to define what the set of possible URLs [looks like](http://blog.mattheworiordan.com/post/13174566389/url-regular-expression-for-links-with-or-without-the).

{% highlight java linenos %}
interface URL {
  String url : url.matches("...") // Look at the link above to see what this mess looks like...
}
{% endhighlight %}

The problem is, you end up embedding the documentation, and in this case, a much less readable version of the entire standard for defining URIs, into the type signature.

> Types help define an interface of a function or a class.

This quote I can agree with. Types do _help_ define an interface, but they're only part of the definition.  Claiming that you can successfully use an interface based on it's type signature alone? I'll stick with reading the documentation.