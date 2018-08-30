---
title: "R Iris Dataset"
output: html_document
layout: post
date: 2015-01-12T00:00:00+08:00
tags: R dataset 
---



## Iris DataSet

Iris DataSet
The data set contains 3 classes of 50 instances each, where each class refers to a type of iris plant. One class is linearly separable from the other 2; the latter are NOT linearly separable from each other.

Predicted attribute: class of iris plant.
<http://archive.ics.uci.edu/ml/datasets/Iris>


{% highlight r %}
data(iris)
head(iris)
{% endhighlight %}



{% highlight text %}
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 3          4.7         3.2          1.3         0.2  setosa
## 4          4.6         3.1          1.5         0.2  setosa
## 5          5.0         3.6          1.4         0.2  setosa
## 6          5.4         3.9          1.7         0.4  setosa
{% endhighlight %}


{% highlight r %}
summary(iris)
{% endhighlight %}



{% highlight text %}
##   Sepal.Length    Sepal.Width     Petal.Length    Petal.Width   
##  Min.   :4.300   Min.   :2.000   Min.   :1.000   Min.   :0.100  
##  1st Qu.:5.100   1st Qu.:2.800   1st Qu.:1.600   1st Qu.:0.300  
##  Median :5.800   Median :3.000   Median :4.350   Median :1.300  
##  Mean   :5.843   Mean   :3.057   Mean   :3.758   Mean   :1.199  
##  3rd Qu.:6.400   3rd Qu.:3.300   3rd Qu.:5.100   3rd Qu.:1.800  
##  Max.   :7.900   Max.   :4.400   Max.   :6.900   Max.   :2.500  
##        Species  
##  setosa    :50  
##  versicolor:50  
##  virginica :50  
##                 
##                 
## 
{% endhighlight %}



{% highlight r %}
str(iris)
{% endhighlight %}



{% highlight text %}
## 'data.frame':	150 obs. of  5 variables:
##  $ Sepal.Length: num  5.1 4.9 4.7 4.6 5 5.4 4.6 5 4.4 4.9 ...
##  $ Sepal.Width : num  3.5 3 3.2 3.1 3.6 3.9 3.4 3.4 2.9 3.1 ...
##  $ Petal.Length: num  1.4 1.4 1.3 1.5 1.4 1.7 1.4 1.5 1.4 1.5 ...
##  $ Petal.Width : num  0.2 0.2 0.2 0.2 0.2 0.4 0.3 0.2 0.2 0.1 ...
##  $ Species     : Factor w/ 3 levels "setosa","versicolor",..: 1 1 1 1 1 1 1 1 1 1 ...
{% endhighlight %}

## Including Plots

### Scatter Plot

{% highlight r %}
plot(x=iris$Petal.Length, y=iris$Petal.Width, col=iris$Species)
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/assets/unnamed-chunk-3-1.svg)

 We could use the pch argument (plot character) for specify the marking of Species. 
 pch=21 is for filled circles, pch=22 for filled squares, pch=23 for filled diamonds, pch=24 or pch=25 for up/down triangles.
 

{% highlight r %}
plot(iris$Petal.Length, iris$Petal.Width, pch=c(23,24,25)[unclass(iris$Species)], main="Iris Data")
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/assets/unnamed-chunk-4-1.svg)

or we can add color to them

{% highlight r %}
plot(iris$Petal.Length, iris$Petal.Width,pch=21, bg=c("red","green3","blue")[unclass(iris$Species)], main="Iris Data")
{% endhighlight %}

![plot of chunk unnamed-chunk-5](/assets/unnamed-chunk-5-1.svg)

#### Scatter Plot Matrix
This shows the possible two-dimensional projections of multidimensional data 

{% highlight r %}
pairs(iris[1:4], col=iris$Species, pch = 21, bg = c("red", "green3", "blue")[unclass(iris$Species)])
{% endhighlight %}

![plot of chunk unnamed-chunk-6](/assets/unnamed-chunk-6-1.svg)

From the plot the Petal.Length and Petal.Width can be best used for classifying the Species. 

This shows the possible two-dimensional projections of multidimensional data. 
We can print the correlation coeff and p value in the top panel.


{% highlight r %}
panel.cor <- function(x, y, digits = 2, cex.cor, ...)
{
  usr <- par("usr"); on.exit(par(usr))
  par(usr = c(0, 1, 0, 1))
  # correlation coefficient
  r <- cor(x, y)
  txt <- format(c(r, 0.123456789), digits = digits)[1]
  txt <- paste("r= ", txt, sep = "")
  text(0.5, 0.6, txt)

  # p-value calculation
  p <- cor.test(x, y)$p.value
  txt2 <- format(c(p, 0.123456789), digits = digits)[1]
  txt2 <- paste("p= ", txt2, sep = "")
  if(p<0.01) txt2 <- paste("p= ", "<0.01", sep = "")
  text(0.5, 0.4, txt2)
}

pairs(iris[1:4], col=iris$Species, pch = 21, bg = c("red", "green3", "blue")[unclass(iris$Species)],
      upper.panel=panel.cor)
{% endhighlight %}

![plot of chunk unnamed-chunk-7](/assets/unnamed-chunk-7-1.svg)

## Using SVM for Classifying the Species

{% highlight r %}
library("e1071")

index <- 1:nrow(iris)
testindex <- sample(index, trunc(length(index)/4))
testset <- iris[testindex,]
trainset <- iris[-testindex,]

svm_model <- svm(Species ~ ., data=trainset)
summary(svm_model)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## svm(formula = Species ~ ., data = trainset)
## 
## 
## Parameters:
##    SVM-Type:  C-classification 
##  SVM-Kernel:  radial 
##        cost:  1 
##       gamma:  0.25 
## 
## Number of Support Vectors:  44
## 
##  ( 8 18 18 )
## 
## 
## Number of Classes:  3 
## 
## Levels: 
##  setosa versicolor virginica
{% endhighlight %}


Predict the testdata with model


{% highlight r %}
prediction <- predict(svm_model,testset[,-5])
tab <- table(pred = prediction, true = testset[,5])
tab
{% endhighlight %}



{% highlight text %}
##             true
## pred         setosa versicolor virginica
##   setosa         11          0         0
##   versicolor      0          9         2
##   virginica       0          0        15
{% endhighlight %}

Tuning SVM for best cost and gamma 

{% highlight r %}
svm_tune <- tune.svm(Species ~ ., data=trainset, 
              kernel="radial", cost=10^(-1:2), gamma=c(.5,1,2))

print(svm_tune)
{% endhighlight %}



{% highlight text %}
## 
## Parameter tuning of 'svm':
## 
## - sampling method: 10-fold cross validation 
## 
## - best parameters:
##  gamma cost
##    0.5    1
## 
## - best performance: 0.05378788
{% endhighlight %}

Run SVM after tuning

{% highlight r %}
svm_model_after_tune <- svm(Species ~ ., data=iris, kernel="radial", cost=1, gamma=0.5)
summary(svm_model_after_tune)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## svm(formula = Species ~ ., data = iris, kernel = "radial", cost = 1, 
##     gamma = 0.5)
## 
## 
## Parameters:
##    SVM-Type:  C-classification 
##  SVM-Kernel:  radial 
##        cost:  1 
##       gamma:  0.5 
## 
## Number of Support Vectors:  59
## 
##  ( 11 23 25 )
## 
## 
## Number of Classes:  3 
## 
## Levels: 
##  setosa versicolor virginica
{% endhighlight %}

Predict the testdata with model


{% highlight r %}
prediction <- predict(svm_model_after_tune,testset[,-5])
tab <- table(pred = prediction, true = testset[,5])
tab
{% endhighlight %}



{% highlight text %}
##             true
## pred         setosa versicolor virginica
##   setosa         11          0         0
##   versicolor      0          9         1
##   virginica       0          0        16
{% endhighlight %}

## K Means clustering


{% highlight r %}
set.seed(20)
irisCluster <- kmeans(iris[, 3:4], 3, nstart = 20)
irisCluster
{% endhighlight %}



{% highlight text %}
## K-means clustering with 3 clusters of sizes 50, 52, 48
## 
## Cluster means:
##   Petal.Length Petal.Width
## 1     1.462000    0.246000
## 2     4.269231    1.342308
## 3     5.595833    2.037500
## 
## Clustering vector:
##   [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
##  [36] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
##  [71] 2 2 2 2 2 2 2 3 2 2 2 2 2 3 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 3 3 3 3 3
## [106] 3 2 3 3 3 3 3 3 3 3 3 3 3 3 2 3 3 3 3 3 3 2 3 3 3 3 3 3 3 3 3 3 3 2 3
## [141] 3 3 3 3 3 3 3 3 3 3
## 
## Within cluster sum of squares by cluster:
## [1]  2.02200 13.05769 16.29167
##  (between_SS / total_SS =  94.3 %)
## 
## Available components:
## 
## [1] "cluster"      "centers"      "totss"        "withinss"    
## [5] "tot.withinss" "betweenss"    "size"         "iter"        
## [9] "ifault"
{% endhighlight %}

Tabulate the clusters by species

{% highlight r %}
table(irisCluster$cluster, iris$Species)
{% endhighlight %}



{% highlight text %}
##    
##     setosa versicolor virginica
##   1     50          0         0
##   2      0         48         4
##   3      0          2        46
{% endhighlight %}
From the table we can detect the mis-classification

Plot the clusters

{% highlight r %}
cluster <- as.factor(irisCluster$cluster)
plot(iris$Petal.Length, iris$Petal.Width,pch=21, bg=c("red","green3","blue")[unclass(cluster)], main="Iris Data")
{% endhighlight %}

![plot of chunk unnamed-chunk-15](/assets/unnamed-chunk-15-1.svg)
You can compare it with the plot of the original data.

## Using RandomForest for classification


{% highlight r %}
library(randomForest)
model.rf <- randomForest(Species ~ . , data=trainset)
model.rf
{% endhighlight %}



{% highlight text %}
## 
## Call:
##  randomForest(formula = Species ~ ., data = trainset) 
##                Type of random forest: classification
##                      Number of trees: 500
## No. of variables tried at each split: 2
## 
##         OOB estimate of  error rate: 5.31%
## Confusion matrix:
##            setosa versicolor virginica class.error
## setosa         39          0         0  0.00000000
## versicolor      0         38         3  0.07317073
## virginica       0          3        30  0.09090909
{% endhighlight %}


{% highlight r %}
pred.rf <- predict(model.rf,testset[,-5])
tab <- table(pred = pred.rf, true = testset[,5])
tab
{% endhighlight %}



{% highlight text %}
##             true
## pred         setosa versicolor virginica
##   setosa         11          0         0
##   versicolor      0          9         2
##   virginica       0          0        15
{% endhighlight %}

tuning Random Forest


{% highlight r %}
tuneRF(trainset[,-5],trainset[,5],ntreeTry=100, 
     stepFactor=1.5,improve=0.01, trace=TRUE, plot=TRUE, dobest=FALSE)
{% endhighlight %}



{% highlight text %}
## mtry = 2  OOB error = 5.31% 
## Searching left ...
## Searching right ...
## mtry = 3 	OOB error = 5.31% 
## 0 0.01
{% endhighlight %}

![plot of chunk unnamed-chunk-18](/assets/unnamed-chunk-18-1.svg)

{% highlight text %}
##       mtry   OOBError
## 2.OOB    2 0.05309735
## 3.OOB    3 0.05309735
{% endhighlight %}



{% highlight r %}
model.rf.tune <-randomForest(Species ~.,data=trainset, mtry=2, ntree=1000, 
     keep.forest=TRUE, importance=TRUE,test=testset)
model.rf.tune
{% endhighlight %}



{% highlight text %}
## 
## Call:
##  randomForest(formula = Species ~ ., data = trainset, mtry = 2,      ntree = 1000, keep.forest = TRUE, importance = TRUE, test = testset) 
##                Type of random forest: classification
##                      Number of trees: 1000
## No. of variables tried at each split: 2
## 
##         OOB estimate of  error rate: 5.31%
## Confusion matrix:
##            setosa versicolor virginica class.error
## setosa         39          0         0  0.00000000
## versicolor      0         38         3  0.07317073
## virginica       0          3        30  0.09090909
{% endhighlight %}



{% highlight r %}
pred.rf.tune <- predict(model.rf.tune,testset[,-5])
tab <- table(pred = pred.rf.tune, true = testset[,5])
tab
{% endhighlight %}



{% highlight text %}
##             true
## pred         setosa versicolor virginica
##   setosa         11          0         0
##   versicolor      0          9         2
##   virginica       0          0        15
{% endhighlight %}

## Neural Network


{% highlight r %}
library(caret)
library(nnet)
model <- train(Species ~ ., trainset, method='nnet', trace = FALSE) # train
# we also add parameter 'preProc = c("center", "scale"))' at train() for centering and scaling the data
model
{% endhighlight %}



{% highlight text %}
## Neural Network 
## 
## 113 samples
##   4 predictor
##   3 classes: 'setosa', 'versicolor', 'virginica' 
## 
## No pre-processing
## Resampling: Bootstrapped (25 reps) 
## Summary of sample sizes: 113, 113, 113, 113, 113, 113, ... 
## Resampling results across tuning parameters:
## 
##   size  decay  Accuracy   Kappa      Accuracy SD  Kappa SD  
##   1     0e+00  0.8049272  0.6910480  0.16023075   0.26135095
##   1     1e-04  0.8423737  0.7573686  0.19609549   0.29686460
##   1     1e-01  0.9244082  0.8856883  0.04276652   0.06368417
##   3     0e+00  0.8907742  0.8302890  0.10494926   0.16537702
##   3     1e-04  0.9548395  0.9311542  0.03295088   0.04937277
##   3     1e-01  0.9568830  0.9342232  0.02642218   0.03999543
##   5     0e+00  0.9343500  0.9005662  0.06765619   0.10017960
##   5     1e-04  0.9511903  0.9256171  0.03516218   0.05280885
##   5     1e-01  0.9568830  0.9342232  0.02642218   0.03999543
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final values used for the model were size = 3 and decay = 0.1.
{% endhighlight %}



{% highlight r %}
prediction <- predict(model, testset[-5])                           # predict
tab <- table(prediction, testset[,5])                                  # compare
tab
{% endhighlight %}



{% highlight text %}
##             
## prediction   setosa versicolor virginica
##   setosa         11          0         0
##   versicolor      0          9         1
##   virginica       0          0        16
{% endhighlight %}

Tuning the nnet for parameter size. Train function from caret package is a good starting point for this

{% highlight r %}
my.grid <- expand.grid(.decay = c(0.5, 0.1), .size = c(2,3,4,5))
model.nnet.tune <- train(Species ~ ., trainset, method='nnet',maxit = 1000, tuneGrid = my.grid, trace = F) 
model.nnet.tune
{% endhighlight %}



{% highlight text %}
## Neural Network 
## 
## 113 samples
##   4 predictor
##   3 classes: 'setosa', 'versicolor', 'virginica' 
## 
## No pre-processing
## Resampling: Bootstrapped (25 reps) 
## Summary of sample sizes: 113, 113, 113, 113, 113, 113, ... 
## Resampling results across tuning parameters:
## 
##   decay  size  Accuracy   Kappa      Accuracy SD  Kappa SD  
##   0.1    2     0.9596507  0.9386234  0.03293291   0.05011685
##   0.1    3     0.9587417  0.9371725  0.03207521   0.04877295
##   0.1    4     0.9597173  0.9386446  0.03296815   0.05012780
##   0.1    5     0.9597173  0.9386446  0.03296815   0.05012780
##   0.5    2     0.9465429  0.9189847  0.04205641   0.06292420
##   0.5    3     0.9479592  0.9212251  0.04220599   0.06335961
##   0.5    4     0.9507754  0.9254962  0.03779758   0.05668289
##   0.5    5     0.9496569  0.9236362  0.03818369   0.05755772
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final values used for the model were size = 4 and decay = 0.1.
{% endhighlight %}


{% highlight r %}
prediction <- predict(model.nnet.tune, testset[-5])                    # predict
tab <- table(prediction, testset[,5])                                  # compare
tab
{% endhighlight %}



{% highlight text %}
##             
## prediction   setosa versicolor virginica
##   setosa         11          0         0
##   versicolor      0          9         1
##   virginica       0          0        16
{% endhighlight %}

## Generalised Boosted Models 
This is available as the method gbm in caret package


{% highlight r %}
fitControl <- trainControl(method="repeatedcv",
                           number=5,
                           repeats=1,
                           verboseIter=TRUE)
set.seed(25)
model.gbm <- train(Species ~ ., data=iris,
                method="gbm",
                trControl=fitControl,
                verbose=FALSE)
{% endhighlight %}



{% highlight text %}
## + Fold1.Rep1: shrinkage=0.1, interaction.depth=1, n.minobsinnode=10, n.trees=150 
## - Fold1.Rep1: shrinkage=0.1, interaction.depth=1, n.minobsinnode=10, n.trees=150 
## + Fold1.Rep1: shrinkage=0.1, interaction.depth=2, n.minobsinnode=10, n.trees=150 
## - Fold1.Rep1: shrinkage=0.1, interaction.depth=2, n.minobsinnode=10, n.trees=150 
## + Fold1.Rep1: shrinkage=0.1, interaction.depth=3, n.minobsinnode=10, n.trees=150 
## - Fold1.Rep1: shrinkage=0.1, interaction.depth=3, n.minobsinnode=10, n.trees=150 
## + Fold2.Rep1: shrinkage=0.1, interaction.depth=1, n.minobsinnode=10, n.trees=150 
## - Fold2.Rep1: shrinkage=0.1, interaction.depth=1, n.minobsinnode=10, n.trees=150 
## + Fold2.Rep1: shrinkage=0.1, interaction.depth=2, n.minobsinnode=10, n.trees=150 
## - Fold2.Rep1: shrinkage=0.1, interaction.depth=2, n.minobsinnode=10, n.trees=150 
## + Fold2.Rep1: shrinkage=0.1, interaction.depth=3, n.minobsinnode=10, n.trees=150 
## - Fold2.Rep1: shrinkage=0.1, interaction.depth=3, n.minobsinnode=10, n.trees=150 
## + Fold3.Rep1: shrinkage=0.1, interaction.depth=1, n.minobsinnode=10, n.trees=150 
## - Fold3.Rep1: shrinkage=0.1, interaction.depth=1, n.minobsinnode=10, n.trees=150 
## + Fold3.Rep1: shrinkage=0.1, interaction.depth=2, n.minobsinnode=10, n.trees=150 
## - Fold3.Rep1: shrinkage=0.1, interaction.depth=2, n.minobsinnode=10, n.trees=150 
## + Fold3.Rep1: shrinkage=0.1, interaction.depth=3, n.minobsinnode=10, n.trees=150 
## - Fold3.Rep1: shrinkage=0.1, interaction.depth=3, n.minobsinnode=10, n.trees=150 
## + Fold4.Rep1: shrinkage=0.1, interaction.depth=1, n.minobsinnode=10, n.trees=150 
## - Fold4.Rep1: shrinkage=0.1, interaction.depth=1, n.minobsinnode=10, n.trees=150 
## + Fold4.Rep1: shrinkage=0.1, interaction.depth=2, n.minobsinnode=10, n.trees=150 
## - Fold4.Rep1: shrinkage=0.1, interaction.depth=2, n.minobsinnode=10, n.trees=150 
## + Fold4.Rep1: shrinkage=0.1, interaction.depth=3, n.minobsinnode=10, n.trees=150 
## - Fold4.Rep1: shrinkage=0.1, interaction.depth=3, n.minobsinnode=10, n.trees=150 
## + Fold5.Rep1: shrinkage=0.1, interaction.depth=1, n.minobsinnode=10, n.trees=150 
## - Fold5.Rep1: shrinkage=0.1, interaction.depth=1, n.minobsinnode=10, n.trees=150 
## + Fold5.Rep1: shrinkage=0.1, interaction.depth=2, n.minobsinnode=10, n.trees=150 
## - Fold5.Rep1: shrinkage=0.1, interaction.depth=2, n.minobsinnode=10, n.trees=150 
## + Fold5.Rep1: shrinkage=0.1, interaction.depth=3, n.minobsinnode=10, n.trees=150 
## - Fold5.Rep1: shrinkage=0.1, interaction.depth=3, n.minobsinnode=10, n.trees=150 
## Aggregating results
## Selecting tuning parameters
## Fitting n.trees = 50, interaction.depth = 3, shrinkage = 0.1, n.minobsinnode = 10 on full training set
{% endhighlight %}



{% highlight r %}
model.gbm
{% endhighlight %}



{% highlight text %}
## Stochastic Gradient Boosting 
## 
## 150 samples
##   4 predictor
##   3 classes: 'setosa', 'versicolor', 'virginica' 
## 
## No pre-processing
## Resampling: Cross-Validated (5 fold, repeated 1 times) 
## Summary of sample sizes: 120, 120, 120, 120, 120 
## Resampling results across tuning parameters:
## 
##   interaction.depth  n.trees  Accuracy   Kappa  Accuracy SD  Kappa SD  
##   1                   50      0.9533333  0.93   0.05055250   0.07582875
##   1                  100      0.9533333  0.93   0.03800585   0.05700877
##   1                  150      0.9533333  0.93   0.03800585   0.05700877
##   2                   50      0.9400000  0.91   0.04944132   0.07416198
##   2                  100      0.9533333  0.93   0.03800585   0.05700877
##   2                  150      0.9400000  0.91   0.04944132   0.07416198
##   3                   50      0.9666667  0.95   0.02357023   0.03535534
##   3                  100      0.9600000  0.94   0.02788867   0.04183300
##   3                  150      0.9533333  0.93   0.02981424   0.04472136
## 
## Tuning parameter 'shrinkage' was held constant at a value of 0.1
## 
## Tuning parameter 'n.minobsinnode' was held constant at a value of 10
## Accuracy was used to select the optimal model using  the largest value.
## The final values used for the model were n.trees = 50, interaction.depth
##  = 3, shrinkage = 0.1 and n.minobsinnode = 10.
{% endhighlight %}



{% highlight r %}
prediction <- predict(model.gbm, testset[-5]) 
tab <- table(prediction, testset[,5])
tab
{% endhighlight %}



{% highlight text %}
##             
## prediction   setosa versicolor virginica
##   setosa         11          0         0
##   versicolor      0          9         0
##   virginica       0          0        17
{% endhighlight %}


