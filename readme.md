Analysis of CUPLA in Columbia University
========================================================
# EDAV Final Project ---Yi Jiang(yj2306)

# Introduction
For my project I decided to apply text analysis skills to derive information from a dataset from CULPA, a website that allows students to review professors from past classes and to track other's opinions on their potential classes for the next semester.

Using CULPA, students can write a review and comment on professor, course's content, and its workload. Other students have the option of agreeing or disagreeing with a particular review, and deciding whether a review is funny. Students also nominate professors for silver or gold nuggets, which are given to professors by CULPA based on their reviews.

# Data
The data from CULPA in JSON format. There are two files in the data, one with professors, and one with reviews. Rjson package in R Studio is applied to convert the JSON objects to R objects. In total there are about 3000 professors and 21000 reviews.The entire [dataset](http://www.columbia.edu/~zjn2101/culpadump.zip) can be found on their website.

# Objective

With this dataset,some exploratory data analysis can be applied to it. Try to ask some questions and look if we can find answer from this dataset. Those questions can be:
1.  Is there any relationship between the workload and the evaluation of a professor?(i.e. the nugget of the professor is "none" or "silver" or "gold")
2.  Are there any difference in words frequency in reviews from different departments?
3.  What's the distribution of nuggets in different departments?
4.  What kind of reviews would get most "agree"/"disagree"/"fun"?
I am not sure whether we can find answere of those questions from this dataset, but I can just do some exploratory analysis to see. 

#Analysis

## Data Cleanup
As for data pre-processing, we used the tm package to build a corpus from character vectors of reviews. Then we applied a number of transformations, including changing letters to lower case, removing punctuations, removing white space, and removing stopwords. The remaining terms then became effective as the textual representation. 
```
load(".Rdata")
texts<-r.review
## vector, each element is a review
require(tm)
require(Snowball)
corpus<-Corpus(VectorSource(texts))
## do some standard processes of cleaning data
corpus <- tm_map(corpus,tolower)
corpus <- tm_map(corpus, removeWords, stopwords("english"))
corpus <- tm_map(corpus,stripWhitespace)
corpus <- tm_map(corpus,removePunctuation)
# generate the document-term matrix
dtm <-DocumentTermMatrix(corpus)
term<-colnames(dtm)
tabu<- tm_term_score(dtm,term,slam::col_sums)
# generate the count for each words in dict
sort.dict<-sort(tabu,decreasing=TRUE)
dict <- names(sort.dict)
sort.dict[1:10]
```
The result:
```
   class    really professor      will  students       one      take    course      just       can 
    51742     13002     12601     10983     10676     10478      9438      9267      8856      8436 
```

After generating a document-term matrix from clean corpus, tasks like classification can be applied directly to it.

For each review, we retrieved the actual textual contents of the review, and workload, the numbers of "agree"/ "disagree"/ "funny review"received by the review, and the nuggets assigned to each professor.

We split the projects into two parts: analysis and prediction. For analysis we looked at the data, trying to characterize behavior across departments in terms of word use, workload declaration. The first predictive objective of this project is to predict whether or not a professor has a nugget and its type based on other variables with a main focus on review and workload. The second predictive goal is to predict whether the review is regarded as funny.



