---
layout: post
title: "Data Processing With Apache Spark"
date: 2016-01-11T00:00:00+08:00
tags: bigdata analytics
---
Spark is a big data processing framework which enables fast and advanced analytics computation over hadoop clusters. 



## Spark  Architecture
A Spark application consists of: a driver program and a list of executors. 

The driver program uses the SparkContext object to coordinate the running of the Spark applications run as independent sets of processes on a cluster

The SparkContext can connect to several types of cluster managers

- Standalone cluster manager 
- Mesos 
- YARN

These cluster managers allocate resources across applications. 
Once connected to the cluster managers, 

#### Spark Application Execution Steps

1. The Spark driver is launched to invoke the main method of the Spark application.
 
2. The driver asks the cluster manager for resources to run the application, i.e. to launch executors that run tasks.

3. The cluster manager launches executors.

4. SparkContext acquires executors on nodes in the cluster, which are processes that run computations and store data for your application. 

5. SparkContext sends your application code (defined by JAR or Python files passed to SparkContext) to the executors. 

6. SparkContext sends tasks to the executors to run.

7. Once SparkContext.stop() is executed from the driver or the main method has exited, all the executors are terminated and the cluster resources are released by the cluster manager.


<img src="{{ site.url }}/assets/spark_cluster.png" alt="sparkcluster" style="width: 400px;"/>

Reference: <http://spark.apache.org/docs/latest/cluster-overview.html>

### Standalone Cluster Manager

Spark provides a simple standalone mode where spark manages the cluster without an external cluster manager. Each node should have the spark binary installed. In the standalone mode we can manually deploy the spark master and workers. 

First we can start the master 

{% highlight bash %}
./sbin/start-master.sh
{% endhighlight %}

The master web UI can seen at  http://localhost:8080 by default. We can connect each of workers to this master by specifying the master address


{% highlight bash %}
./bin/spark-class org.apache.spark.deploy.worker.Worker spark://IP:PORT
{% endhighlight %}

We can also launch the spark standalone cluster with a launch script by specifying the hostnames of all the Spark worker machines in the conf/slaves file in Spark directory

To further configure the spark cluster edit conf/spark-env.sh

- Set the SPARK_WORKER_INSTANCES which determines the number of Worker instances (#Executors) per node (its default value is only 1)
- Set the SPARK_WORKER_CORES # number of cores that one Worker can use 
- Set SPARK_WORKER_MEMORY  # total amount of memory that can be used on one machine (Worker Node) for running Spark programs.

Copy this configuration file to all Worker Nodes, on the same folder and start the cluster by running sbin/start-all.sh

Various details to configure the master and slaves are documented quite well at the doc
<https://spark.apache.org/docs/1.2.1/spark-standalone.html>


### Yarn Cluster Manager

<img src="{{ site.url }}/assets/yarnflow.png" alt="yarnflow" style="width: 450px;"/>

Reference: <http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/>

There are two deploy modes that can be used to launch Spark applications on YARN.

- Cluster Mode
- Client Mode

#### YARN Cluster Mode

In cluster mode, the Spark driver runs inside an application master process.The application master itself runs on one of the node managers in the cluster which is managed by YARN on the cluster. The client can go away after initiating the application. 


While running the application in cluster mode, the driver runs on a different machine than the client, so SparkContext.addJar won't work out of the box with files that are local to the client. To make files on the client available to SparkContext.addJar, include them with the --jars option in the launch command.

#### YARN Client Mode
In this mode the driver program is running on the yarn client where we input the command to submit the spark application. It may not be a machine in the yarn cluster. In this mode, although the drive program is running on the yarn client machine, the tasks are executed on the executors in the node managers of the YARN cluster and the application master is only used for requesting resources from YARN.

#### Which mode to use

While deciding which mode to use one the factors to consider is the latency between the drivers and executors. 
If you are submitting the client application from your laptop and the network latency to worker nodes from your laptop is not high, then you can use the client mode. Else use the cluster mode so that drivers and workers and in co-located space to reduce the latency.
Instead of submitting applications from your local machine, it is also a good idea to submit it from a gateway machine that is colocated with the worker nodes.

When the --master parameter is yarn, the ResourceManager’s address is picked up from the Hadoop configuration. Later in the configuration setup the location of these hadoop configuration files need to be provided.

#### Default Ports

Spark Standalone master UI : 8080 

SparkContext web UI: 4040

Spark History Server: 18080

HDFS Namenode: 50070

Yarn Resource manager :8088

#### Spark History Server

The Spark History Server displays information about the history of completed Spark applications. It provides application history from event logs stored in the file system. It periodically checks in the background for applications that have finished and renders a UI to show the history of applications by parsing the associated event logs.

You can start the history server by executing:
{% highlight bash %}
./sbin/start-history-server.sh
{% endhighlight %}

The Web interface for the spark history server is at port 18080

Various other options are there to configure the history server in managing logs. Example 
spark.history.fs.cleaner.maxAge can be set to 7d to retain only last 7 days logs etc

Ref: <http://spark.apache.org/docs/latest/monitoring.html>

#### Spark Infrastructure

One of the main advantage of Spark is much faster data processing as compared to Hadoop. Spark does its processing in memory so to leverage on this we should add more RAM and CPU to the spark infrastructure. The typical recommendation are

- 4-8 disks per node, where each disk is 1-2 TB
- 8-16 cores per node
- 32 GB or more memory each node. 75% of this for Spark and rest for OS and other applications

While development you can also determine how much memory is used for a certain dataset size. Load part of your dataset in a Spark RDD and use the Storage tab of Spark's monitoring UI (<http://<driver-node:4040>) to see its size in memory.

## Spark Installation

While installing spark the objective here is to get it running in standalone mode and over yarn client. This is a development cluster used for local testing.

My current stack on mac is the following 

Jdk1.8 , scala 2.11, hadoop 2.7.1

#### Download 

http://spark.apache.org/downloads.html

Current version of spark is 1.6.1

#### Build

Running Spark on YARN requires a Spark build which is built with YARN support. 

Binary distributions can be downloaded from the downloads page of the project website or you can build it yourself.

{% highlight bash %}
build/sbt -Pyarn -Phadoop-2.x assembly
{% endhighlight %}

The various options for compiling spark are described here

<https://spark.apache.org/docs/latest/building-spark.html>

<http://spark.apache.org/downloads.html>


#### Configuration

{% highlight bash %}
cp conf/spark-env.sh.template conf/spark-env.sh
conf/spark-env.sh
{% endhighlight %}

#### Edit the conf file with following content

{% highlight bash %}
# Have to add hadoop conf so as to let spark know about hadoop or yarn configuration files.
# This would be required when connecting spark with yarn
export HADOOP_CONF_DIR=${HADOOP_CONF_DIR:-"/etc/hadoop"}
export SPARK_MASTER_IP=localhost 
export SPARK_WORKER_CORES=1
export SPARK_WORKER_MEMORY=800m
export SPARK_WORKER_INSTANCES=1

# create the path to logs file
mkdir <PATH TO LOGS FOLDER>/logs

cp slaves.template slaves
sudo cp conf/log4j.properties.template conf/log4j.properties

#Edit the log4j.properties file with following content
# Initialize root logger
log4j.rootLogger=INFO, FILE
# Set everything to be logged to the console
log4j.rootCategory=INFO, FILE
# Ignore messages below warning level from Jetty, because it's a bit verbose
log4j.logger.org.eclipse.jetty=WARN
# Set the appender named FILE to be a File appender
log4j.appender.FILE=org.apache.log4j.FileAppender
# Change the path to where you want the log file to reside
log4j.appender.FILE.File=<PATH TO LOGS FOLDER>/logs/SparkOut.log
# Prettify output a bit
log4j.appender.FILE.layout=org.apache.log4j.PatternLayout
log4j.appender.FILE.layout.ConversionPattern=%d{yy/MM/dd HH:mm:ss} %p %c{1}: %m%n


{% endhighlight %}

#### Run Spark Shell in Standalone mode

bin/spark-shell

#### Run hdfs from hadoop installation folder

./sbin/start-dfs.sh

namenode UI should be up at <http://localhost:50070/dfshealth.html#tab-overview>

#### run yarn  from hadoop installation folder

./sbin/start-yarn.sh

Yarn Cluster manager UI should be up at http://localhost:8088/cluster

bin/spark-shell --master yarn

Spark jobs can be seen at http://localhost:4040/jobs/


{% highlight bash %}

# Calculate Pi

./bin/run-example org.apache.spark.examples.SparkPi 

# Calculate Pi in cluster mode
bin/spark-submit --class org.apache.spark.examples.SparkPi --master yarn --deploy-mode cluster examples/target/scala-*/spark-*.jar 10

# Calculate Pi in client mode
bin/spark-submit --class org.apache.spark.examples.SparkPi --master yarn --deploy-mode client examples/target/scala-*/spark-*.jar 10
#The output will be sent to the console

{% endhighlight %}

To view the output result "Pi is roughly 3.140784" while running in cluster mode, go to the Yarn Cluster manager UI (http://localhost:8088/cluster). Here you can see the Pi application marked as finished. Click on the Application ID and open the logs file stdout

#### MLlib Correlations example: 

./bin/run-example org.apache.spark.examples.mllib.Correlations 

#### MLlib Linear Regression example: 

{% highlight bash %}
./bin/spark-submit --master yarn --class org.apache.spark.examples.mllib.LinearRegression examples/target/scala-*/spark-*.jar data/mllib/sample_linear_regression_data.txt  

spark-submit over yarn client

/spark-submit --master yarn --class org.apache.spark.examples.mllib.LinearRegression examples/target/scala-*/spark-*.jar data/mllib/sample_linear_regression_data.txt  

{% endhighlight %}
#### Once the work is done, stop the cluster

{% highlight bash %}
#stop yarn from hadoop installation folder
sbin/stop-yarn.sh
#stop dfs from hadoop installation folder
sbin/stop-dfs.sh

{% endhighlight %}


Refer to the official doc for running jobs on Yarn

<http://spark.apache.org/docs/latest/running-on-yarn.html>

Spark History Server


## Spark Programming

In Spark the computations are expressed in terms of actions and transformations on RDD (Resilient Distributed Datasets). The RDD in turn is an immutable collection of objects. Each RDD is split into multiple partitions which may be computed on different nodes of the cluster. Operations over RDD are automatically parallelized over the cluster. The RDD’s can be persisted in memory which helps in creating a fast pipeline of operations without costly disk seeks. 

#### Driver
Every Spark application consists of a driver program that launches various parallel operations on a cluster. Driver programs access Spark through a SparkContext object, which represents a connection to a computing cluster. Once you have a SparkContext, you can use it to build RDDs Example : the call to sc.textFile creates an RDD.
To run various operations on the RDD you can call functions on them. Example  the count operation. There are more than 80 high level functions that we can use to query the data.

{% highlight bash %}
val lines = sc.textFile(“Example.txt”)
lines.count
{% endhighlight %}

Once created, RDDs offer two types of operations: transformations and actions. 

- Transformations construct a new RDD from a previous one. 
- Actions, on the other hand, compute a result based on an RDD

Spark computes RDD only in a lazy fashion—that is, the first time they are used in an action.
RDD are recomputed each time you want to run an action on it. To persist an RDD you have to use the persist action. Persisting RDD on disk, instead of the memory is also possible.


To Parallize a collection, or a dataset that is already in memory, you can  parallize() method. The elements of this collections are copied to an RDD and operated in parallel

{% highlight bash %}
val input = sc.parallelize(List(1,2,3,4,5))
{% endhighlight %}

Use the aggregate function to do parallel computation in different function and provide custom function to combine the results of computation from different threads

{% highlight bash %}
# eg sum of the numbers
input.aggregate( (0,0))(
	(x,y) => ( x._1 + y, x._2 + 1)
	(x,y) => ( x._1 + y._1 , x._2 + y._2)
  )
{% endhighlight %}

Some functions are available only on certain types of RDDs, such as mean() and variance() on numeric RDDs or join() on key/value pair RDDs.

#### Pair RDD
Pair RDD's are RDD containing key value pairs.
Pair RDDs have a reduceByKey() method that can aggregate data separately for each key, and a join() method that can merge two RDDs together by grouping elements with the same key. 
i
{% highlight bash %}
#Creating a pair RDD using the first word as the key and then filtering based on the second element
val pairs = lines.map(x => (x.split(" ")(0), x))
pairs.filter{case (key, value) => value.length < 20}

#Word Count in scala
val input = sc.textFile("s3://...")
val words = input.flatMap(x => x.split(" "))
val result = words.map(x => (x, 1)).reduceByKey((x, y) => x + y)

# per key average
input.combineByKey(
v => (v,1)
(acc: (Int,Int), v :Int) => ( acc._1 + v, acc._2 + 1 ),
(acca:(Int,Int), acc2(Int,Int)) => ( acc1._1 + acc2._1 , acc1._2 + acc1._2)
).map({ case (key, value) => (key, value._1/value._2)}).map(println(_))

{% endhighlight %}

#### Grouping
Instead of doing groupByKey and then applying a map function to do an operation on list of values in a key, do the reduceByKey which is more efficient (as it avoids the middles step of creating a list of keys)


{% highlight bash %}
val items = sc.parallelize( Seq( ("a",5) , ("a",9), ("b",4), ("c",10))
items.groupByKey().map( x => (x._1 , x._2.sum)).collect()
{% endhighlight %}
This is inefficient and better use reduceByKey method




