---
layout: post
title: "Topic Model With R"
date: 2015-12-17T00:00:00+08:00
tags: R nlp
---

Given a corpus we can use topic modelling to get insights into the structure of information embedded in the docs. LDA is topic modelling algorithm that can be used for this purpose.

LDA is a [generative algorithm](/2013/12/14/discriminative-vs-generative-classifiers.html) that assumes documents as a bag of words where each document has mixture of topics and each topic has a discrete probability distribution of words. In LDA the topic distribution is assumed to have a Dirichlet prior which gives a smoother topic distribution per document.

Topic models in general try to represent the document with a set of topic vectors.

{% highlight bash %}
library(“topicmodels”)
#LDA based on gibbs method
ldaObj <-LDA(dtm,k, method="Gibbs", control=list(nstart=nstart, seed = seed, best=best, burnin = burnin, iter = iter, thin=thin))

#Get LDA top Topic terms for docs
ldaOut.topics <- as.matrix(topics(ldaObj))

#Get the topic probability distribution for the docs
gammaDF <- as.data.frame(ldaObj@gamma)
{% endhighlight %}

With the LDA object we can get top terms by topics or get the probability distribution of topics for each docs.

While running the LDA model, model tuning is required to fit the model find the values which best describes the data. 

References

- [LDA intuition by quora user](https://www.quora.com/What-is-a-good-explanation-of-Latent-Dirichlet-Allocation)

