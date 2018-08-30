---
layout: post
title: "Corpus Analysis With R"
date: 2014-12-24T00:00:00+08:00
tags: R analytics nlp
---

The tm package provides functionality for exploratory analysis on  text corpus.
The nature of text corpus has a big effect on the data modeling. Some of the text corpus that i have worked on in past includes

- Structured news feeds
- Unstructured html news webpages
- Web page Comments
- Tweets
- Posts on news sharing forum corpus
- Emails

After the text preprocessing stage all of these would be a list of documents. But the nature of data in each of these vary a lot. HTML pages have to go through rigorous content processing to filter out unwanted content. Comments and tweets will have short text where each of them will have there own semantic keywords. Similarly a corpus of emails will have to handled in specific ways. 

After the preprocessing stage we are ready to explore the corpus to further find the relationships between the docs and understand the deeper meaning

{% highlight bash %}
# create the text corpus which tm can operate
 corpus <- Corpus( textVectors)

# do the required transformations on the corpus
 corpus <- tm_map(corpus,stripWhitespace)
 corpus <- tm_map(corpus,content_transformer(tolower))
 corpus <- tm_map(corpus,removeNumbers)
 corpus <- tm_map(corpus,removePunctuation,preserve_intra_word_dashes = TRUE)
# instead of using the default stopwords you can add your own stopwords
 corpus <- tm_map(corpus,removeWords, stopwords("english"))
 corpus <- tm_map(corpus, PlainTextDocument)
# you can create custom transformation by supplying your function to the tm_map function

#Create a term document matrix from the corpus to do numerical bag of words computation of the corpus
tdm <- TermDocumentMatrix(corpus)

#Or  Instead of frequency you can use a tfidf score as the weighting functions
tdm <- TermDocumentMatrix(corpus,control = list(weighting = function(x)
    weightTfIdf(x, normalize = FALSE)))

{% endhighlight %}

Now that we have converted a corpus into a numerical matrix we can do all kind of computation on it. The term document matrix is a sparse matrix since many of the words would be unique to certain docs. Use the inspect function to look into the details of this matrix

inspect(tdm)

Some operations you could do on this

{% highlight bash %}
# subset the matrix only by the words contained in a tagset
tdm.onlytags <- tdm[rownames(tdm) %in% tagset$tag, ]

# create a sorted list of top terms
top_terms <- sort(sapply(rownames(tdm), function(x) sum(tdm[c(x), ])), decreasing = TRUE)
top_terms <-  sort(rowSums(tdm))

# create a term document matrix from the top terms in corpus
tdm[c(names(top_terms)), ]
{% endhighlight %}

Since this is numerical matrix, you can slice and dice it to your hearts content.

Some other functionality provided by tm package

{% highlight bash %}
# remove sparse terms from term document matrix
removeSparseTerms(dtm, 0.1)

# find the freq terms above a threshold
￼findFreqTerms(dtm, lowfreq=1000)

# find Associations with a word with correlation limit
findAssocs(dtm, "data", corlimit=0.6)

{% endhighlight %}

Often we want to do sentence level analyis of a give corpus. The text could be a article, book or a corpus where breaking it into sentences and then doing a word level analysis into the sentence structure could give a better understanding of the document. 

For doing these kind of NLP analysis we use the packages NLP, OpenNLP and RWeka

{% highlight bash %}
install.packages(c("NLP", "openNLP", "RWeka", "qdap"))
# download the OpenNLP models
install.packages("openNLPmodels.en", repos = "http://datacube.wu.ac.at/", type = "source")
{% endhighlight %}

The package openNLPmodels.en contains the following models for identifying these entities from text :

- Date
- Location
- Money
- Organization
- Percentage
- Person
- Time


{% highlight bash %}
library(NLP)
library(openNLP)
library(RWeka)
library(magrittr)
# read the plain text dataset
# http://www.gutenberg.org/cache/epub/1342/pg1342.txt
lines <- readLines("pride_and_prejudice.txt")
doc <- as.String(paste(lines, collapse = " "))

# create the sentence annotator
sent_ann <- Maxent_Sent_Token_Annotator()
# create the word annotator
word_ann <- Maxent_Word_Token_Annotator()
# create person annotator
person_ann <- Maxent_Entity_Annotator(kind = "person")
# create location annotator
location_ann <- Maxent_Entity_Annotator(kind = "location")
# create organization annotator
organization_ann <- Maxent_Entity_Annotator(kind = "organization")
# create the annotation pipeline
doc_annotations <- annotate(doc, list(sent_ann, word_ann,person_ann,location_ann,organization_ann))
doc_ann <- AnnotatedPlainTextDocument(doc, doc_annotations)

{% endhighlight %}

Now we can extract the various entities from the extracted document for further analysis

