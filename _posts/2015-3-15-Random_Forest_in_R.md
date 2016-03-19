---
layout: post
title: Random Forest in R
---

Random forests were formally introduced by Breiman in 2001. Due to his excellent performance and simple application, 
random forests are getting a more and more popular modeling strategy in many different research areas. 

Random forests are suitable in many different modeling cases, such as classification, regression, survival time analysis, 
multivariate classification and regression, multilabel classification and quantile regression. 

A overview of existing random forest implementation and their speed performance can be found in 
the [ranger documentation](http://arxiv.org/pdf/1508.04409v1.pdf), altough this list is not exhaustive and many new implementations are comming up. 
The performances of models build with different packages slightly differ, depending on how the random forest algorithm was 
implemented. 

Now I will present some random forest implementation in R. A good site to find all r packages to one specific topic is 
[Metacran](http://www.r-pkg.org/).

{{ post.excerpt }}

A statistic about the RStudio downloads of different R-packages for executing the random forest 
can be seen in the table. It was created with help of the R-package cranlogs.
**randomForest** is clearly the most used package in R, probably because it was the first available already in April 2002. 
 
 **package**              |  **RStudio downloads in the last month**
 -------------        |  -------------:
1:   randomForest     | 28671
2:       party        | 13512
3:  randomForestSRC   | 2134
4:        ranger      | 1405
5:      Rborist       | 341
6:          bigrf     |   13

So what package to use? Of course it depends on the statistical problem.  
In the classical classification or regression case you have many options. For big datasets the packages 
**ranger** or **Rborist** should be used, because they are much faster or **randomForest.ddR**, an extension of 
randomForest. Wright (the author of **ranger**) recommends to use **Rborist** for 
low dimensional data with large sample sizes (n>25,000) and **ranger** in all other cases. 
The core of **ranger** is written with help of the R-package **Rcpp** and it generally produces the same results as **randomForest**. 

## Multivariate Classification and Regression

Multivariate classifications and regressions with random forests can be modelled with **randomForestSRC**. 
The multivariate classification case in **randomForestSRC** is used in **mlr** package to perform multilabel classifications with random forests, see the [mlr tutorial](http://mlr-org.github.io/mlr-tutorial/devel/html/multilabel/index.html) for more information. 
Moreover in the **randomForestSRC** package many hyperparameters can be set individually like for example the splitting 
rule. 

## Survival Analysis

If you want a random forest for survival analysis, **ranger** or **randomForestSRC** can be used.
**party** contains the function **cforest** which implements the random forest and bagging ensemble algorithms utilizing 
conditional inference trees as base learners. 

## Quantile Regression

For quantile regression you can use the package **quantregForest**, which is 
based on the **randomForest** package. This implementation could also be used for estimating conditional 
densities and conditional probability distributions. 

## More

Many more packages exist with new algorithms based on random forests (**RRF**, **roughrf**, **icRSF** (for survival), **wsrf**, **iRafnet**, **randomUniformForest**, **fuzzyforest**), possibilities for variable selection (**varSelRF**, **VSURF**, **RFgroove**, **AUCRF**), visualisations (**ggRandomForests**, **forestFloor**) or imputation (**missForest**, **imputeMissings**) with random forests. 

This implementations can be found easily by a quick search on [Metacran](http://www.r-pkg.org/search.html?q=random%20forest&page=1).
