---
layout: post
title: "Predictive Analytics Project Workflow"
date: 2015-12-24T00:00:00+08:00
tags: analytics bigdata
---
There are three main stages in a predictive analytics project

1. Requirement gathering
2. Data modeling
3. Delivery and deployment


<img src="{{ site.url }}/assets/analytics_workflow.png" alt="analytics_workflow" style="width: 450px;"/>

### Requirement Gathering
While working out the requirements for a predictive analytics project it is important to define the objectives clearly. Care should be taken to avoid making the objectives overly broad or overly specific. For example, if we are measuring the customer churn at an online portal, we can define the task as the percentage of customers who do not access for a period of 3 months after continuous weekly activities. 

The "No Free Lunch Theorm" in Machine Learning states that no one model is optimal in absence of any knowledge about the problem. So everthing starts with understanding the problem and then selecting the model to fit the data. Infact defining the objectives is one part of the problem we are solving. Another aspect is to deep dive into the domain context to better understand the nature of dataset features.

Once the objectives are clearly defined we can map the requirements for the dataset we need to get.  In the customer churn example, the data set for customers should not have the anonymous visitors, or logged in users who haven’t shopped yet from the website.  All the assumption in preparing the dataset should should again be confirmed with the project owner. 

Along with the dataset, we need to define the performance metrics for the models and minimum threshold of acceptable performance that is good enough. These performance goals are the project metrics that should be acceptable to the project owner so that we have clear target to achieve. The performance thresholds should be realistic, taking into consideration the complexity of the problem we are addressing. Predictive models are never perfect and there could be endless optimization loop if we don’t agree on these early on.

The amount of data we need to collect would depend on the models and level of accuracy that we are targeting. Simpler models based on linear regression etc may need less features and data than other complex models.

### Data Modeling
Once we have the data,  we need to process it to map to our requirement. During this stage, we do exploratory data analysis to better understand the data. This include understanding the various features and their relationships, the data quality, missing elements and whether any imputations can be done. Algorithms like KNN can help impute the missing value by making intelligent guesses on them. We visualize the features by plotting them and try to understand the relationship between among them.
We would also need to transform the data into proper units and scales so that they could be comparable. Some of the algorithms require the data to be in numerical or categorical and we have appropriately transform the features. We also have to identify the outliers and other problematic features in the data like redundant features and features that have no variance etc.

While exploring the data we have to choose the features which are suitable for building predictive models. The number of features define the dimensions of the model. Higher number of dimensions will make the models complex. It will increase the computation time and also would mean that we need larger data set to learn from all the possible combination of features. 

To reduce the number of features we can combine or remove some of the features. We can use methods like  PCA, which create a set of new features which are a linear combination of existing features thus reducing the number features. The first principal component of PCA can be visualized as the line in the feature space along which the data varies most. The second principal component is the next line, which is uncorrelated from the first line and along which data varies most and so on. Since PCA is based on variance of data points, we need to scale the features before applying the method.

To build our predictive models  we choose a model based on various constraints. 

- The nature of the task could define whether regression, classification or clustering models is required
- Is the application time sensitive. 
- Do we need to update the model frequently. How much time it takes in training the models
- As the size of data increases, would we be able to scale the computation

It is better to start with simpler models like NaiveBayes and linear/logistic regression etc and get a base performance. It also gives a measure of how far we are from our target accuracy and becomes the base from where further iterations are done. We can create different models by varying the feature sets and applying different methods. We can also create models which are combination of other models.  

The models are then tested for accuracy in prediction. Accuracy tests depend on the type of model. For regression models it could be the RMSE, for classification would be the confusion matrix, precision, recall and fscore.

Models are then further optimised to improve their precision or computation speed. 
 
### Deployment 
At this stage the models and the results are presented for project delivery. If the next stage involves deployment of models into a production system then this would involve working together with the software team to integrate the models into their existing workflow. 

There are various options for integration. If the model is built using R then we can save the model object and this binary object is then loaded in a new R session with latest data for making predictions. This could be a batch job done over hadoop cluster or single machine. In stream processing environment it would be better to decouple the prediction part from main workflow to make the system more responsive. You could even have a prediction api as a service to decouple this so that this api service could be independently scaled on demand.

However be the deployment scenario, it is important to capture the metrics of predicted results for continuous model improvement.

