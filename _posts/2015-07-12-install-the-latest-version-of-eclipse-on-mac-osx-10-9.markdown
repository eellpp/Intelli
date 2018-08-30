---
layout: post
title: "Install the Latest Version of Eclipse on Mac OSx 10.9"
date: 2015-07-12T00:00:00+08:00
tags: troubleshooting devops
---
On OS X 10.9 version the mac the default installtion of java is 1.6. But eclipse installation asks for higher version of java.
However oracle updater downloads the latest version of the java but does not install at /usr/bin/java. So eclipse installer cannot find the latest version even though it may be installed.

# check if latest version of JDK is installed
The JDK version are installed at "/Library/Java/JavaVirtualMachines/"
This should contain the latest version (1.8 now)
jdk1.8.0_73.jdk. Else install the latest version of jdk and make sure the version exists here

{% highlight bash %}
# point to the jdk java binary
sudo rm /usr/bin/java
sudo ln -s  /Library/Java/JavaVirtualMachines/jdk1.8.0_73.jdk/Contents/Home/bin/java usr/bin/java
#check the java version
java -version
javac --version
{% endhighlight %}

Errors i faced during the process:

"The JVM shared library "/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/bin/../lib/server/libjvm.dylib" does not contain the JNI_CreateJavaVM symbol"

