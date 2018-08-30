---
layout: post
title: "rJava Load Error on MacOSX"
date: 2015-05-15T00:00:00+08:00
tag: troubleshooting
---
I am trying to use the OpenNLP , Weka packages for NLP analysis. These require the package rJava to be loaded. While loading rJava i was getting this error

> Error : .onLoad failed in loadNamespace() for 'rJava', details:
>
>  call: dyn.load(file, DLLpath = DLLpath, ...)
>
>  error: unable to load shared object '/Library/Frameworks/R.framework/Versions/3.1/Resources/library/rJava/libs/rJava.so':
>  dlopen(/Library/Frameworks/R.framework/Versions/3.1/Resources/library/rJava/libs/rJava.so, 6): Library not loaded: @rpath/libjvm.dylib
>
>  Referenced from: /Library/Frameworks/R.framework/Versions/3.1/Resources/library/rJava/libs/rJava.so
>
>  Reason: image not found
>
> Error: package or namespace load failed for 'rJava'

It is trying to look for libjvm.dylib which is at '/Library/Java/JavaVirtualMachines//jdk1.8.0_73.jdk/Contents/Home/jre/lib/server/libjvm.dylib'

So as a solution for this i loaded it with the path

{% highlight bash %}
dyn.load('/Library/Java/JavaVirtualMachines//jdk1.8.0_73.jdk/Contents/Home/jre/lib/server/libjvm.dylib')
library(rJava)
library(NLP)
library(openNLP)
{% endhighlight %}
