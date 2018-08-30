---
layout: post
title: "Public Datasets for Analysis"
date: 2015-07-09T00:00:00+08:00
tags: nlp R
---

### Model Datasets for Learning

Select some datasets which are well understood. We should be know the problem context for which the data was collected and the outcomes. It should also be well published so that the nature of underlying data is well known. While working with these model datasets our goal is to reduce the unknowns so that we could focus on evaluating the algorithms and tools. 
These datasets should be of smaller size so that the computation could be done easily in memory on development machines. Data sets can be segmented by

1. Number of attributes: Few attributes and large number of samples and large number of attributes along with relatively smaller data set
2. Type of algorithm: Whether the dataset is suited for classification, regression , clustering or other kind of analysis
3. Nature of data: Categorical, Numerical, Ordinal or mixtures

There can be numerous other ways to segment datasets based on nature of investigation. Choose the one that fits your problem the best.

Some of popular data sets

- Binary Classification: 
[Adult Data Set ](http://archive.ics.uci.edu/ml/datasets/Adult)

Predict whether income exceeds $50K/yr based on census data. Also known as "Census Income" dataset.

- Multi-Class Classification: [Iris Data Set](http://archive.ics.uci.edu/ml/datasets/Iris)
- Regression: [Wine Quality Data Set](http://archive.ics.uci.edu/ml/datasets/Wine+Quality)
- Categorical Attributes: [Breast Cancer Data Set](http://archive.ics.uci.edu/ml/datasets/Breast+Cancer)
- Integer Attributes: [Computer Hardware Data Set](https://archive.ics.uci.edu/ml/datasets/Computer+Hardware)
- Classification Cost Function: [German Credit Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))
- Missing Data: [Horse Colic Data Set](https://archive.ics.uci.edu/ml/datasets/Horse+Colic)


### General Statistics

UCI Public Datasets

<http://archive.ics.uci.edu/ml/datasets.html>


### NLP

Various corpus that i found useful for NLP analysis

#### Major English Language General Corpora

<http://www.uow.edu.au/~dlee/corpora.htm>

#### Reuters Corpus

<http://trec.nist.gov/data/reuters/reuters.html>

#### CoNLL corpus for NER

<http://www.cnts.ua.ac.be/conll2003/ner>

The  2002  and  2003 CoNLL  shared  tasks  provided  manually  annotated  datasets  for English and other languages. Due to copyright issues only the annotations were made available at CONLL 2003 and to build the complete datasets it is necessary to access the Reuters Corpus, which can be obtained from NIST for research purposes.

#### Entities from the RCV1 corpus
<http://jmlr.csail.mit.edu/papers/volume5/lewis04a/>

#### WikiGold corpus

<http://schwa.org/projects/resources/wiki/Wikiner#WikiGold>

Manually annotated dataset of wikipedia pages.

