---
layout: post
title:  "Clojure json and array map"
categories: coding
tags: clojure, tdd, json
short: >
  Clojure has an interesting way of having keys as different types
---

In clojure you can have 3 different key types (as far as i have
encountered them)

Keyword keys
{% highlight clojure %}
{ :keyword "value" }
{% endhighlight %}

Unevaluated form
{% highlight clojure %}
{ 'unevaluated "value" }
{% endhighlight %}

String
{% highlight clojure %}
{ "string" "value" }
{% endhighlight %}

As I did not know about string keys I was stuck with
ring.middleware.json/wrap-json-response because whenever i tried to
{% highlight clojure %}
(get { "string" "value" } :string)
(get { "string" "value" } 'string)
{% endhighlight %}

It yield no result... And in repl you would see this map represnted as
{% highlight bash %}
{ string value }
{% endhighlight %}
It does not matther whether you evaluate string key or unevaluated form
key
{% highlight clojure %}
{ 'string value }
{ "string" value }
{% endhighlight %}
So that was the reason why it was so confusing

Anyway problem was solved by doing

{% highlight clojure %}
(get { "string" "value" } "string")
{% endhighlight %}

Although I think, maybe it would be better to make keywords from
string keys by calling clojure.walk/keywordize-keys
{% highlight clojure %}
(:string (clojure.walk/keywordize-keys { "string" "value" }))
{% endhighlight %}
?

