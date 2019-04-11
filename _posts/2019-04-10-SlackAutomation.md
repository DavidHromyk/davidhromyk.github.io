---
layout: post
title: Slack automation with Jenkins
subtitle: Automate slack messages with Jenkins builds
tags: [Slack]
comments: true
---

![Crepe](http://inn.spb.ru/images/100/DSC100139148.jpg)

So here goes my first tutorial blog. Recently I had the idea to automate sending slack messages once a week without my intervation. I figured it would be great if I could laissez-faire my Slack account and let my automation work wonders.

### __Prerequisites__

In order for this setup to work there are a few prereqesuites that need to be met. 

* An Ubuntu 18.04 server on [Digital Ocean](https://www.digitalocean.com/)
* Integration with [Slack's API](https://api.slack.com/)

Here's a code chunk:

~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.
