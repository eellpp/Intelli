---
layout: post
title: "Hadoop Ecosystem"
date: 2016-01-11T00:00:00+08:00
tags: hadoop bigdata
---
### Hadoop Installation on Mac

#### Installation
Download the latest version of hadoop binaries and extract it in local folder

On Mac you can also install with the brew command
> brew install hadoop
(The current version at writing was 2.7.3)
This  installs hadoop at /usr/local/Cellar/hadoop/2.7.3

The current JDK version was 1.8 and the java home was set up as /Library/Java/JavaVirtualMachines/jdk1.8.0_73.jdk/Contents/Home
It is a good practice to set this up in .bashrc so that it could be picked up all your JVM based apps

#### Configuration

Edit hadoop-env.sh

{% highlight bash %}
> vim etc/hadoop/hadoop-env.sh
The JAVA_HOME should be set as below in file
export JAVA_HOME="$(/usr/libexec/java_home)"
{% endhighlight %}

SSH
Mac: Enable Remote Login in System Preference -> Sharing.

ssh and check that you can ssh to the localhost without a passphrase:

{% highlight bash %}
$ ssh localhost
If you cannot ssh to localhost without a passphrase, execute the following commands:

$ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
$ cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
{% endhighlight %}


Edit following config files in your Hadoop directory

{% highlight bash %}
1) etc/hadoop/core-site.xml:

<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
2) etc/hadoop/hdfs-site.xml:

<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
3) etc/hadoop/mapred-site.xml:

<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
4) etc/hadoop/yarn-site.xml:

<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
{% endhighlight %}

#### Execution
Format and start HDFS and YARN
> hdfs namenode -format
> start-dfs.sh

Now you can browse the web interface for the NameNode at - http://localhost:50070/

Make the HDFS directories required to execute MapReduce jobs:

{% highlight bash %}
> hdfs dfs -mkdir /user
> hdfs dfs -mkdir /user/{username} #make sure you add correct username here
{% endhighlight %}

#### Start ResourceManager daemon and NodeManager daemon:

{% highlight bash %}
> start-yarn.sh
{% endhighlight %}

#### Browse the web interface for the ResourceManager at - http://localhost:8088/

Test examples code that came with the hadoop version
{% highlight bash %}
> bin/hdfs dfs -mkdir /input
> bin/hdfs dfs -put libexec/etc/hadoop /input

> hadoop jar libexec/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar grep /input/* /output 'dfs[a-z.]+'
{% endhighlight %}


#### Examine the output files:

Copy the output files from the distributed filesystem to the local filesystem and examine them:

{% highlight bash %}
> hdfs dfs -get /output ./output
> vim ./output/part-r-00000
{% endhighlight %}

#### submit a yarn job

{% highlight bash %}
> yarn jar libexec/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar pi 6 1000
{% endhighlight %}

When you're done, stop the daemons with:

{% highlight bash %}
$ stop-yarn.sh
$ stop-dfs.sh
{% endhighlight %}

Reference: http://zhongyaonan.com/hadoop-tutorial/setting-up-hadoop-2-6-on-mac-osx-yosemite.html
