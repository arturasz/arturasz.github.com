---
layout: post
title:  "iPhone safari has a bug when using fixed element in overflow div while in an iframe"
categories: coding
tags: javascript iPhone html react
---

Moving fixed element out of the overflow: scroll div fixes erratic behaviour

We had 2 elements which had fixed positioning.

{% highlight html %}
  <iframe>
    ...
     <div class="scrollable">
       <div class="fixed">
       </div>
     </div>
    ...
  </iframe>
{% endhighlight %}

And while scrolling those elements where jumping eratically. And that only happened if your widget was in a scrolling iframe.
The solution was quite simple, move the fixed element out of scrollable div!

{% highlight html %}
  <iframe>
    ...
     <div class="scrollable">
     </div>
     <div class="fixed">
     </div>
    ...
  </iframe>
{% endhighlight %}

One of elements had to be inside the scrollable div, because it was part of a bigger component in React and I did not want to put it outside.
This was solved easily with a Gateway/Portal 
<a href="https://github.com/cloudflare/react-gateway" target="_blank">https://github.com/cloudflare/react-gateway</a>
