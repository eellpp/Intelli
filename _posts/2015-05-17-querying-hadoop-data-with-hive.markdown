---
layout: post
title: "Querying Hadoop Data With Hive"
date: 2015-05-17T00:00:00+08:00
tags: hadoop bigdata hive
---

Hive provides SQL like query interface to Apache Hadoop. Hive in turn does the computation over large number of nodes on hadoop cluster to provide the results. This enables doing easy ad-hoc data analysis and summarization queries. 

Hive does not support index queries like RDBMS, but unlike other relational database it scales very well. Hive is not designed for real time queries and row level updates. It is best suited to batch jobs over large sets of immutable data like web logs. 

#### Running Hive in Hadoop Cluster

Inside the hadoop cluster the data is distributed over by HDFS. You can run hive queries from namenode or any of the data nodes.

#### Hive Metastore and tables

Hive uses a metastore,a database, to store information about the tables it knows. Hive tables are just information about where the data is stored and in which format.

Hive has two kind of tables

- managed
- externa

#### Managed Table

Managed tables data is controlled by Hive. It will create a directory for the data on HDFS and when we drop the table it will delete the data.

When you create a table in hive, the default location is inside the directory "/user/hive/warehouse/".
You can load the data into table by

{% highlight bash %}
CREATE TABLE mytablename(id INT, something STRING) ROW FORMAT
              DELIMITED FIELDS TERMINATED BY ','
	    LINES TERMINATED BY '\n' STORED AS TEXTFILE;
{% endhighlight %}

This will create the directory "/user/hive/warehouse/mytablename"

{% highlight bash %}
LOAD DATA INPATH '/path/to/data/rawdata' INTO TABLE mytablename;
{% endhighlight %}

When you drop the table, the raw data is lost as the directory corresponding to the table in warehouse is deleted. 

#### External Table
To create external table, simply point to the location of data while creating the tables and mark the table "EXTERNAL". This will ensure that the data is not moved into a location inside the warehouse directory.

HiveQL is Hive's query language and is dialect of SQL. 

{% highlight bash %}
CREATE EXTERNAL TABLE mytablename(id INT, something STRING) ROW FORMAT
              DELIMITED FIELDS TERMINATED BY ','
	    LINES TERMINATED BY '\n' STORED AS TEXTFILE
	    LOCATION '/path/to/mydata';;
{% endhighlight %}

When you drop the table the data at the location is not deleted.

You can have multiple tables all pointing to the same raw data

{% highlight bash %}
hive -e 'SELECT * FROM departments'
# suppress the message and show only results
hive -S -e 'SELECT * FROM departments' 
hive -S -e 'SELECT * FROM departments where size > 10 AND location = "NY" AND budget IS NOT NULL' 
{% endhighlight %}

#### SortBy Vs Orderby
Orderby requires one reducer to sort the final output. If the number of rows in the output is too large, the single reducer could take a very long time to finish.

SortBy sorts the rows before feeding the rows to a reducer.

The difference between "order by" and "sort by" is that the former guarantees total order in the output while the latter only guarantees ordering of the rows within a reducer. If there are more than one reducer, "sort by" may give partially ordered final results.

{% highlight bash %}
hive -S -e 'SELECT * FROM departments order by size DESC' 
#this sorts the data by reducer and not globally, which can be much faster for large data sets.
hive -S -e 'SELECT * FROM departments sort by size DESC' 
show tables
{% endhighlight %}

#### Distribute By And Cluster By

Hive uses the columns in Distribute By to distribute the rows among reducers. All rows with the same Distribute By columns will go to the same reducer. However, Distribute By does not guarantee clustering or sorting properties on the distributed keys.

Cluster By is a short-cut for both Distribute By and Sort By. Clustering the sorting would provide a tremendous performance improvement since the sort can potentially be done by hundreds of cluster nodes in parallel.

Ref : <https://cwiki.apache.org/confluence/display/Hive/LanguageManual+SortBy>

{% highlight bash %}
hive -S -e 'SELECT * FROM departments Distribute By location
hive -S -e 'SELECT * FROM departments Distribute By location SORTBY location,size
hive -S -e 'SELECT * FROM departments CLUSTER BY location
{% endhighlight %}


#### Join Tables

{% highlight bash %}

CREATE TABLE school (name STRING, location STRING) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';
CREATE TABLE student (name STRING, school STRING) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';

SELECT sc.name
FROM school sc
JOIN student st
ON sc.name= st.school;

{% endhighlight %}

#### Storage Formats

TEXTFILE

Text file format can load data of form CSV (Comma Separated Values), delimited by Tabs, Spaces and JSON data.By default if we use TEXTFILE format then each line is considered as a record.

SEQUENCEFILE

Sequence files are flat files consisting of binary key-value pairs which can be split by hadoop and distributed across map jobs. The main use of these files is to club two or more smaller files and make them as a one sequence file.

There are three types of sequence files:
- Uncompressed key/value records.
- Record compressed key/value records :only values’ are compressed here
- Block compressed key/value records – both keys and values are collected in ‘blocks’ separately and compressed.

RCFILE

RCFILE stands of Record Columnar File which is another type of binary file format which offers high compression rate. This column oriented storage is very useful while performing analytics. It is easy to perform analytics when we “hive’ a column oriented storage type.

ORCFILE

ORC stands for Optimized Row Columnar which means it can store data in an optimized way than the other file formats and is also optimized for speed.

Ref: <https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFileFormat>

#### Compression

Data stored in Gzip or Bzip2 can be directly imported into a table stored as TextFile. The compression will be detected automatically and the file will be decompressed on-the-fly during query execution

However data in compressed format cannot be split into chunks/blocks to run multiple maps in parallel.The recommended practice is to insert data into another table, which is stored as a SequenceFile.

{% highlight bash %}

CREATE TABLE raw (line STRING)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n';
 
CREATE TABLE raw_sequence (line STRING)
   STORED AS SEQUENCEFILE;
 
LOAD DATA LOCAL INPATH '/tmp/weblogs/20090603-access.log.gz' INTO TABLE raw;
 
SET hive.exec.compress.output=true;
SET io.seqfile.compression.type=BLOCK; -- NONE/RECORD/BLOCK (see below)
INSERT OVERWRITE TABLE raw_sequence SELECT * FROM raw;

{% endhighlight %}

Ref: <https://cwiki.apache.org/confluence/display/Hive/CompressedStorage>

#### Using Sqoop 

{% highlight bash %}
# dump data to text file and load it into hive table
echo 'data' > /tmp/dummy.txt
hive -e 'create table dummy (value String);'
Load data local inpath '/tmp/dummy.txt' overwrite into table dummy
describe formatted dummy

#load data into hive using sqoop
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

Tables are stored as directories under Hive’s warehouse directory, which is controlled by the hive.metastore.warehouse.dir property and defaults to /user/hive/warehouse.
You can also create external tables where you point hive to location of data storage.

{% highlight bash %}
create external table external_table (dummy string) location '/user/cloudera/external_table';
soad data inpath '/user/cloudera/data.txt' into table external_table;
drop table external_table
{% endhighlight %}

When you drop an external table, Hive will leave the data untouched and only delete the metadata.



