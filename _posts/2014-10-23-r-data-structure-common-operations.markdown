---
layout: post
title: "R Data Structure Common Operations"
date: 2014-10-23T02:00:00+08:00
tags: R analytics
---

The common data structure in R are 
Vectors, List, Array, Matrix and Dataframe.

###Vector Operations
{% highlight bash %}
# values from 1 :5
vec <- c(1:5)
vec <- c(sample(5))
vec <- rnorm(5,mean=2.5,sd=2)
vec <- c(letters[1:5])
vec_name <-  c("id" = 1, "age" = 21)
vec_name <-  c("id" = 1:5, "age" = 21:25)
 id1  id2  id3  id4  id5 age1 age2 age3 age4 age5 
  1    2    3    4    5   21   22   23   24   25 

# check whether value exists in vector
2 %in% vec
#filtering on multiple conditions 
l <- sample(c(1:50),10)
d <- sample(c(1:50),10)
l[l %in% d | l > 40]
# find the index of the value in vector
match(3,vec)

Selecting elements
# select element 1 and 3
vec[c(1,3)]
# filter out element 1 and 3
vec[-c(1,3)]
# filter out elements based on logical true or false
vec[sample(rep(c(TRUE,FALSE),2), 2)]
# filter out 
vec[vec >3]

# change the value of some elements
vec[c(1,4)] <- c(10,14)
vec[-c(2,3,5)] <- c(10,14)
{% endhighlight %}


### List

{% highlight bash %}
List is a collection of different objects
list( c(1:5), letters[1:5] )
{% endhighlight %}

### Array and Matrices
{% highlight bash %}
mat <- matrix(1:4,nrow = 2, ncol = 2)
arr <- array(1:6,dim=c(2,3))
{% endhighlight %}

### Data frames
{% highlight bash %}
#By default the strings columns are considered as factors. You can turn it to false
data.frame(id = 1:10, category = letters[1:10],stringsAsFactors = FALSE)
> str(df)
'data.frame':	10 obs. of  2 variables:
 $ id      : int  1 2 3 4 5 6 7 8 9 10
 $ category: chr  "a" "b" "c" "d" ...
{% endhighlight %}

Since data frame is both a list and rectangular structure you can access the values in two ways

- You can use the list operators: df[i], df[[i]] , df$col
- You can use the matrix notation: df[i, j] df[i , ] df[ , j]

#### Data.frame vs as.data.frame
If your data is captured in several vectors and/or factors, use the data.frame function to assemble them into a data frame:
> df <- data.frame(v1, v2, v3, f1, f2)

If your data is captured in a list that contains vectors and/or factors, use instead
as.data.frame:
> dfrm <- as.data.frame(list.of.vectors) 
data.frame calls as.data.frame internally


#### Operations on Dataframe

{% highlight bash %}
#Adding a column
cbind(df, data.frame( cat_id = paste(6,"_",letters[6], sep="")))

#Adding a row
rbind(df,data.frame(id = 6, category = letters[6]))

Check out the structure of data frame 
str(mtcars)

# add a column with some NA value
mtcars_df <- cbind(mtcars, data.frame( "temp" = sample(rep(c("a","b",NA,"c"),8),32)  ))

{% endhighlight %}

#### Subsetting dataframe

{% highlight bash %}
# get rows 1:5
mtcars[c(1:5) ,]

# filter out the rows for which the temp column is NA
mtcars_df[ is.na(mtcars_df$temp) == FALSE ,]

#In the dataset lets find the rows where the carb column is 1 or 2
mtcars[mtcars$carb %in% c(1,2)  ,]
subset(mtcars, carb %in% c(1,2) )
mtcars[!is.na(match(mtcars$carb,c(1,2)   )),]

#There are various ways of doing subsetting operations. But the important thing is to know the tricks of using the operators so that when the need arises you can intelligently combine them.

#To group dataframe by factor levels
df <- Toothgrowth
split(df,levels(df$supp)) # this will give the lists of dataframes, one for each factor level
split(mtcars,mtcars$gear)

#To run a function under each of the split group
# get average mpg for different gear levels
 by(mtcars,mtcars$gear,FUN = function(df){ mean(df$mpg)})
mtcars$gear: 3
[1] 16.10667
mtcars$gear: 4
[1] 24.53333
mtcars$gear: 5
[1] 21.38


#Show the freq table for two different columns of a dataframe
f <- mtcars
f$cyl = as.factor(f$cyl)
f$gear = as.factor(f$gear)
table(f$cyl,f$gear)
   
     3  4  5
  4  1  8  2
  6  2  4  1
  8 12  0  2

{% endhighlight %}

### Binning of data
You have a vector, and you want to split the data into groups according to intervals.

{% highlight bash %}
# get a weighted sample 
vec <- sample(c(1:5),20,replace = TRUE, prob = c(10,15,45,25,5))
# create the group ranges
breaks <- c(0,2,3,4,6)
groups <- cut(vec,breaks,c("0-2","2-3","3-4","4-6"))
> table(groups)
groups
0-2 2-3 3-4 4-6 
  4   5  10   1 
{% endhighlight %}

### Operations on model object
Given an model we can update it with new features
{% highlight bash %}
# add additional feature z to existing model m
update(m , ~ . + z) 
#Where . means  "what was previously in this part of the formula".

# update the model by adding new observations in the dataframe df
update(m1, . ~ ., data = df)

#remove a field from model
update(m1,as.formula(paste(".~.-", fieldRemove)) )
{% endhighlight %}


