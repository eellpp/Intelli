---
layout: post
title: "Methods for Evaluating Model Performance"
date: 2015-02-08T00:00:00+08:00
tags: R analytics
---
The methods used to evaluate the prediction accuracy of models depend on the type of models involved.

#### Regression models
In regression models we are dealing with numeric values. We are interested in knowing how far away are our predicted values from the expected values. For this purpose we calculate the Root Mean Square Error (RMSE). This is the square root of the average of the square of errors.
We evaluate the model based on the RMSE 

#### Classification Models
In classification models we are interested in knowing whether the classification was correct.  For this purpose we create a confusion matrix. This is a table is correct and incorrect classified items. 

Some utility functions like the postResample() method from caret package measures the accuracy and kappa statistics of the predictive model.
					
Kappa = (Observed Accuracy − Expected Accuracy) /( 1− Expected Accuracy )

#### Binary Classification Models.
It is common for binary classification models to create the confusion matrix and show the precision and recall of the model. 

* Precision = True Positives/ ( True Positives + False Positives)
* Recall = True Positives / (True Positives + False Negatives)
* F1Score =Precision * Recall / ( Precision + Recall)

