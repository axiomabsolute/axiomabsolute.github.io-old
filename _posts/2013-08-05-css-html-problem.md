---
layout: post
title: Not Quite Separate Content and Presentation
post-custom-css: [flex-box-demo.css]
published: false
---

{{ include-css }}

<!-- excerpt-begin -->
A demonstration of the FlexBox layout mode.
<!-- excerpt-end -->

Let's say we have the following markup

{% highlight html linenos %}
<nav>
    <ul>
        <li class="home-nav-link"><a href="#">Home</a></li>
        <li class="blog-nav-link"><a href="#">Blog</a></li>
        <li class="about-nav-link"><a href="#">About</a></li>
        <li class="contact-nav-link"><a href="#">Contact Us</a></li>
    </ul>
</nav>
{% endhighlight %}

Which renders naturally as:

<nav id="unordered">
    <ul>
        <li class="home-nav-link"><a href="#">Home</a></li>
        <li class="blog-nav-link"><a href="#">Blog</a></li>
        <li class="about-nav-link"><a href="#">About</a></li>
        <li class="contact-nav-link"><a href="#">Contact Us</a></li>
    </ul>
</nav>

But we'd like to style it so that the `About` page is last.  We can add the following style:

{% highlight scss linenos %}
/* Define a flexbox container */
.flex-container {
    display: -webkit-box;
    display: -moz-box;
    display: -ms-flexbox;
    display: -webkit-flex;
    display: flex;

    /* Make it a flexrow */
    flex-flow: row nowrap;
    align-content: center;
    align-items: left;
}

/* Define what it means to be a flex item */
.flex-item {
    order: 0;
    flex: 0 1 auto;
    align-self: auto;
}

/* Make the UL a flex-container */
#ul {
    @extend .flex-container;
}

#ul li {
    @extend .flex-item;
}

/* Now, change the ordering! */
.about-nav-link {
    order: 1;
}
{% endhighlight %}

Now let's see it in action

<nav id="ordered">
    <ul>
        <li class="home-nav-link"><a href="#">Home</a></li>
        <li class="blog-nav-link"><a href="#">Blog</a></li>
        <li class="about-nav-link"><a href="#">About</a></li>
        <li class="contact-nav-link"><a href="#">Contact Us</a></li>
    </ul>
</nav>

Even without all the vendor prefixing, that's a sizable chunk of SCSS just to reorder an element.  Why is this such a big deal?  