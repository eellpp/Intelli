---
layout: post
title: "Scala Project Kickstart at Console"
date: 2015-06-14T00:00:00+08:00
tags: scala
---

Use giter8 to build a basic project structure
<https://github.com/n8han/giter8>

{% highlight bash %}
#Install giter8 on OSX
brew install giter8
{% endhighlight %}

Giter8 provides templates to kickstart the projects.  The list of templates can be found on their github page

https://github.com/n8han/giter8/wiki/giter8-templates


{% highlight bash %}
#Applying a simple template to kickstart 
g8 chrislewis/basic-project
{% endhighlight %}

Provide the basic information that it asks in the prompt
Go to the project directory and check out the build.sbt to verify the content and modify if required.

{% highlight bash %}
# compile 
sbt compile

# run 
sbt run
{% endhighlight %}

### Testing with scala test
{% highlight bash %}
# run all the test
sbt test

# run all the test
sbt test-only org.acme.RedSuite org.acme.BlueSuite

# run a test with pattern
sbt test-only test-only *RedSuite
{% endhighlight %}


Reference 

- <http://www.scalatest.org/user_guide/using_scalatest_with_sbt>



