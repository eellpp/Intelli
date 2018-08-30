---
layout: post
title: "Hadoop Architecture Overview"
date: 2015-08-05T00:00:00+08:00
tags: hadoop bigdata
---
Hadoop and bigdata are synonymous. This is because when you are processing data at a scale at which it is innefficient to process it on a single machine, you have to consider running it over a multiple machines or compute clusters. Hadoop framework provides tools to enable this by promising to do two things : manage the infrastructure and running of jobs split across machines and delivering the results. 

Hadoop architecture consists of distributed storage engine HDFS and a resource management layer (eg YARN) on top of it. The resource management layer abstracts the computing cluster and provides a uniform interface for multiple applications to submit their jobs. These applications could be traditional map reduce apps, spark application, Hive, Solr and others. 

<img src="{{ site.url }}/assets/hadoop_arch.png" alt="hadoop" style="width: 350px;"/>

#### HDFS
HDFS is a distributes file system that sits on top of the existing file system of each node. It is not POSIX complaint. As a distributed system it is designed for failure and handles it by replication of data. Data in HDFS is stored in blocks. The default block size is 128 MB. Each block is replicated over multiple nodes. The default replication factor is 3. When a file is stored in HDFS, it could be split into multiple blocks which could be across nodes. 

Instead of providing hadoop with large number of small files, it is better to have larger file sizes. By having fewer files of large sizes, the data would be store sequentially and there would be fewer disk seeks. 

Hadoop can be configured for rack awareness. It means that it would try to store data in ways to reduce the network bandwidth of cross rack IO

#### YARN
YARN is hadoop resource management layers which provides two functionalities
- generic resource management
- workload management
It supports having multiple application share a common infrastructure running concurrant jobs. Using YARN it is also possible to have multi-tenant hadoop infrastructure where you can provide service level guarantees for tenants. 


<img src="{{ site.url }}/assets/yarn_arch.png" alt="yarn" style="width: 450px;"/>

##### Resource Manager
The generic resource manager runs as a master deamon, usually on a dedicated machine, that  arbitrates the available resources between competing applications. The resource manager knows the current status of the cluster and decides the resources that are allocated to an application. 

##### Application Master
There is a per-application application master which is responsible for execution of all the task within the application. This application master process gets created when the user submits job to application manager. Application master is responsible for monitoring tasks, restarting failed tasks and in general the application specific features. 

##### Node Manager
Each node in the cluster has a node manager and launches containers when requested by the application master. The application master and its tasks are run within these containers. The node manager monitors the containers untilization and notifies the resource manager on available resources. It does not manage application specific map-reduce tasks, which are totally abstracted away by the application master.

#### Application execution steps

1. A client application submits the application.
2. Resource manager checks the cluster status and requests for container from a node manager to run application master. It then launches the application master.
3. Application master requests for containers for running the application tasks. 
4. Node manager provides the containers to application master
5. Application code within the container provides the status to its application master
6. When the application is complete, the application master deregisters with node manager and its containers are deallocated. 

























