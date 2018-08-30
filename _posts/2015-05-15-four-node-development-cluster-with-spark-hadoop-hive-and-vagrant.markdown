---
layout: post
title: "Four Node Development Cluster With Spark,Hadoop,Hive and Vagrant"
date: 2015-05-15T00:00:00+08:00
tags: hadoop spark bigdata
---

I wanted to have set up a hadoop development cluster on MacBook Pro which i can tinker. It was around the same time that i upgraded my Macbook Pro memory to 16GB and so it helps. 

The project is at GitHub.

<https://github.com/eellpp/spark-yarn-hadoop-cluster-vagrant>

Vagrant project to spin up a cluster of 4 nodes with 64-bit CentOS6.5 Linux virtual machines with Hadoop v2.6.0, Spark v1.6.1. and Hive v1.2.1

This is suitable as a quick hands-on and develpoment cluster for playing on the hadoop stack. Memory Requirement: 16GB RAM. Tested on MacBook Pro with 16GB of RAM.

Cluster Configuration:

node1 : HDFS NameNode + Spark Master
node2 : YARN ResourceManager + JobHistoryServer + ProxyServer
node3 : HDFS DataNode + YARN NodeManager + Spark Slave
node4 : HDFS DataNode + YARN NodeManager + Spark Slave
Hive is installed on all nodes

