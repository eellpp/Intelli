---
layout: post
title: "JVM, Scala, Eclipse and Other Related Troubleshooting"
date: 2014-09-28T00:00:00+08:00
tags: troubleshooting
---
Eclipse error: The specified JRE installation does not exist

Go to Eclipse -> Preferences -> Java -> Installed JRE's and add this path

{% highlight bash %}
/Library/Java/JavaVirtualMachines/jdk1.8.0_73.jdk/Contents/Home/
{% endhighlight %}

where: jdk1.8.0_73.jdk should be the installed jdk in your mac.


