---
layout: post
title: Easy Jekyll Excerpts with Liquid Filters
---

Go ahead and look at this page's source.  Go on, do it! Notice the little excerpt comments?

{% highlight html linenos %}
    <!-- excerpt-begin -->

    <!-- excerpt-end -->
{% endhighlight %}

<!-- excerpt-begin -->
Early on in my journey to learn [Jekyll] [2], sleep deprived but determined to figure out what all the fuss was about, I decided that I wanted a way to indicate small sections of each post to feature on the main page.  A quick Google search pointed me to a couple of Jekyll plugins that would get the job done, but [one guy] [3] mentioned another possible solution; [Liquid Filters] [1].
<!-- excerpt-end -->

Liquid is a secure, stateless template engine used by Jekyll to provide basic logic and looping behavior to the static pages it generates.  In addition, Liquid includes filters - operations for manipulating both numerical and string based content.  The original functionality described in the post was eventually added as a built in feature of Jekyll; the parser looks for all text up until the first occurrance of `excerpt_separator` (a YAML front-matter property which defaults to `\n\n`) and sets that to the post's `excerpt` property.  I decided to expand on the concept the author outlined to provide slightly different functionality.

The first thing I did was add a second tag, allowing the excerpt to appear anywhere in the page.  Notice that in this page, the excerpt isn't right at the beginning of the page, but can potentially come after other content, like a graphic or, in this case, a small code block.

Extracting the excerpts for the main page was as simple as filtering the content down to the section I wanted by looking for the tags.  No plugins, no custom code, just a bit of convention!  Knowing my habits, however, at some point I'll forget to add the `<!-- excerpt-* -->` tags, so I decided to add some default behavior, truncating the content after a certain number of words.

{% highlight html linenos %}
    {% raw %}
    {% if post.content contains '<!-- excerpt-begin -->' && post.content contains '<!-- excerpt-end -->' %}
        <div class="row">
            <div class="small-12 large-12 columns">
                <p><em>excert</em></p>
            </div>
        </div>
        <div class="row">
            <div class="small-12 large-12 columns">
                {{ post.content | split: '<!-- excerpt-begin -->' | last | split: '<!-- excerpt-end -->' | first }}
            </div>
        </div>
        <div class="row">
            <div class="small-12 large-12 columns">
                <p><em><a href="{{ post.url }}">full article</a></em></p>
            </div>
        </div>
    {% else %}
        <div class="row">
            <div class="small-12 large-12 columns">
                {% if post.truncate %}
                    <p>{{ post.content | truncatewords: post.truncate }}</p>
                {% else %}
                    <p>{{ post.excerpt }}</p>
                {% endif %}
                <em><a href="{{ post.url }}">continue...</a></em></p>
            </div>
        </div>
    {% endif %}
    {% if forloop.index != forloop.length %}
        <hr/>
    {% endif %}
    {% endraw %}
{% endhighlight %}

To make this behavior a little more configurable, a little logic around the truncation method first looks for a `truncate` variable defined in the post's YAML front-matter.  Finally, if that's not found, it falls back to the default `excerpt` functionality.

Using this technique, we can choose what content to include in the excerpt, including not just text but fully formatted portions of the overall post, including images and other media.  We could even use this technique to create a custom excerpt for the post that doesn't appear anywhere in the article itself, like creating a custom tag line to grab the readers attention, selectively displaying the content in the `excerpt-*` block by comparing the `page.url` to the `post.url`.

When displaying anything but very basic HTML with this technique, you have to be a little careful.  The content of the tag block is pulled, but not the _styles_ applied outside of inline styles, which can lead to content being awkwardly layed out or displayed.  A simple way to mitigate this risk is to stick to site-wide styles, such as those referenced by the default layout or provided by the underlying client side framework (in my case, the [Foundation] [4] framework).

I'm just getting started with Jekyll, but I'm beginning to appreciate the relative simplicity at the core of its philosophy.  I'm interested to see how far the community will push this new wave of interest in static websites, and what lessons they've yet to learn from the forefathers of the web, before the advent dynamic sites.

_Examples:_
* [Built in behavior](/examples/excerpt-separator.html)
* [Truncate after fixed number of words](/examples/truncate-at-80.html)
* [Including media in the excerpt](/examples/templates-everywhere-picture.html)

[1]: https://github.com/Shopify/liquid/wiki/Liquid-for-Designers
[2]: http://jekyllrb.com/
[3]: http://foldl.me/2012/jekyll-excerpts/
[4]: http://foundation.zurb.com/