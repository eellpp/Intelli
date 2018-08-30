---
layout: post
title: "Hadoop data import/export with Sqoop"
date: 2015-08-20T00:00:00+08:00
tags: hadoop bigdata
---
Apache Sqoop is an open source tool that allows to extract data from a structured data store into Hadoop for further processing. In addition to writing the contents of the database table to HDFS, Sqoop also provides you with a generated Java source file (widgets.java) written to the current local directory. Sqoop is a client command and there is no daemon process for it. It depends on HDFS and YARN and database drivers to which it connects.

### Output Formats
By default, Sqoop will generate csv  files for the imported data. It can also write the data as SequenceFiles, Avro datafiles or Parquet files. These binary formats allow data to be compressed while retaining MapReduce’s ability to process different sections of the same file in parallel.


### Splitting columns
 Using metadata about the table, Sqoop will guess a good column to use for splitting the table (typically the primary key for the table, if one exists). The minimum and maximum values for the primary key column are retrieved, and then these are used in conjunction with a target number of tasks to determine the queries that each map task should issue

Eg if table is 100,000 rows and with -m 5, then there would be 5 map-reduce task and each map-reduce task will run a part of query in range : say 10K to 20K etc



{% highlight bash %}
# check if you can connect to mysql with sqoop (using the provided jdbc driver)
sqoop list-databases --connect “jdbc://quickstart.cloudera:3306” --username retail_dba --password cloudera

# list the tables in database
sqoop list-tables --connect “jdbc://quickstart.cloudera:3306/retail_db” --username retail_dba --password cloudera


# import all tables
Sqoop import-all-tables -m 12  --connect “jdbc://quickstart.cloudera:3306/retail_db” --username retail_dba --password cloudera  --warehouse-dir=/user/cloudera/imported_data

{% endhighlight %}

-m means 12 mappers. This means that there are 12 threads active on a single table. All the tables are imported in sequential manner one after the other. It is just a suggestion to hadoop and while during operation hadoop will decide the number of threads to use based on the size of the data, block size etc. If in the output you see files like part-001 to part--006 it means that it has used only 6 threads. By default the number of mappers is 4
If the database there is primary index with min value of 1 and max value of 12, then since there are 12 mappers, sqoop will break the data in 12 parts and issues 12 different queries . One in each thread. Eg: min 1, max 2 , min 2 and max 3 etc. Each of these will be given to each of the mappers to process.


The mappers count is decided based on the volume of the data and nodes available.
References:

- [How many maps and reduces](https://wiki.apache.org/hadoop/HowManyMapsAndReduces)
- [Hadoop number of mappers and reducers](http://stackoverflow.com/questions/20307404/hadoop-number-of-mappers-and-reducers)
- [Good way to decide number of mappers and reducers](https://www.quora.com/What-are-good-ways-to-decide-number-of-reducers)


{% highlight bash %}
# use the eval command to run the select, insert etc
sqoop  eval --connect “jdbc://quickstart.cloudera:3306/retail_db” --username retail_dba --password cloudera 
--query “select * from departments”
{% endhighlight %}

### Importing data to hive
Using scoop we can import data to Hive so that we can run SQL queries with a bigger dataset over a cluster.

{% highlight bash %}
# start the hive shell
create database sqoop_import
dfs -ls /user/hive/warehouse
show databases

# create the hive table 
sqoop import-all-tables \
  --num-mappers 1 \
  --connect "jdbc:mysql://quickstart.cloudera:3306/retail_db" \
  --username=retail_dba \
  --password=cloudera \
  --hive-import \
  --hive-overwrite \
  --create-hive-table \
  --compress \
  --compression-codec org.apache.hadoop.io.compress.SnappyCodec \
  --outdir java_files

# check out the new folders created
> show tables

# validate the count of rows imported
select count(1) from order_items;
sqoop eval --connect “jdbc:mysql://quickstart.cloudera:3306/retail_db” --username cloudera --password cloudera --query “select count(1) from order_items”


# run queries
select * from departments

{% endhighlight %}

### Using Boundary Query and columns
The --boundary-query allow you more control while importing data in parallel. Boundary Query lets you specify an optimized query to get the max, min  else it will try to find the min and max on the query statement and when there are millions of rows this may take a while and slow it down.

{% highlight bash %}
sqoop import \
  --connect "jdbc:mysql://quickstart.cloudera:3306/retail_db" \
  --username=retail_dba \
  --password=cloudera \
  --table departments \
  --target-dir /user/cloudera/departments \
  -m 2 \
  --boundary-query "select 2, 8 from departments limit 1" \
  --columns department_id,department_name

{% endhighlight %}

### Using -split-by

By default during table import the primary key column is used to split and distribute the values from table across the mappers uniformly. However in case of doing a more advanced query, you'll need to specify the column to do the parallel split.

{% highlight bash %}
sqoop import \
  --connect "jdbc:mysql://quickstart.cloudera:3306/retail_db" \
  --username=retail_dba \
  --password=cloudera \
  --query="select * from orders join order_items on orders.order_id = order_items.order_item_order_id where \$CONDITIONS" \
  --target-dir /user/cloudera/order_join \
  --split-by order_id \
  --num-mappers 4

{% endhighlight %}

### Export
While calculating number of mappers for scoop exports, the number of blocks in which files is divided is used as the criteria. There is no difference in importing from hdfs and hive directories. Same commands are use.

{% highlight bash %}
sqoop export --connect "jdbc:mysql://quickstart.cloudera:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table departments_test \
  --export-dir /user/hive/warehouse/departments_test \
  --input-fields-terminated-by '\001' \
  --input-lines-terminated-by '\n' \
  --num-mappers 2 \
  --batch \
  --outdir java_files \
  --input-null-string nvl \
  --input-null-non-string -1

{% endhighlight %}

input-null-non-string : specify what int value can be stored as null . In the query above its -1
Input-null-string :  specify what value in file should be stored as null . In the query above its nvl






