---
title: "New xgboost defaults"
layout: post
output:
  html_document: default
  pdf_document: default
comments: yes
---

**xgboost** is the most famous R package for gradient boosting and it is since long time on the market. 
In one of my [publications](http://www.jmlr.org/papers/volume20/18-444/18-444.pdf), I created a framework for providing defaults (and tunability measures) 
and one of the packages that I used there was **xgboost**. The results provided a default with 
the parameter *nrounds*=4168, which leads to long runtimes. 

Hence I wanted to use the data used in the paper to set *nrounds* to 500 and optimize the other 
parameters to get optimal defaults. 

<!--excerpt-->

I used the same method as mentioned in the paper with the 38 classification datasets and obtained the following (rounded) defaults:

| nrounds|       eta| subsample|booster | max_depth|
|-------:|---------:|---------:|:-------|---------:|
|     500|     0.052|      0.87|gbtree  |        11|

| min_child_weight| colsample_bytree| colsample_bylevel|
|----------------:|----------------:|-----------------:|
|             1.75|             0.71|              0.64|

Now I wanted to know how well this defaults perform on other tasks. So I took a collection of 29 regression tasks of different 
domains, that I collected from different sources such as [OpenML](https://www.openml.org/) and compared my 
defaults against the defaults of **xgboost** with **nrounds**=500. 

The results were not really mindblowing. Here you can see performance graphs for two different measures (R-Squared and Spearman's Rho):

![graphic](/images/xgboost_500_rsq_results.png "graphic")

![graphic](/images/xgboost_500_spearman_results.png "graphic")

It is visible that my defaults perform better than the package defaults in most of the cases regarding the R$^2$ and also regarding 
Spearman's Rho (a nonparametric measure). 

The average values can be seen in the following table:

|             | xgboost_old_default| xgboost_my_default|
|:------------|-------------------:|------------------:|
|R-squared    |               0.614|              0.645|
|Spearman Rho |               0.775|              0.780|

The percentage of datasets where my new defaults are better than the old defaults is 66% for R$^2$ and 62% for Spearman's Rho. 

So all in all my new defaults do not provide a very big performance gain (on average 0.03 or R$^2$), but seem to be better 
than the package defaults. 

In my [paper](http://www.jmlr.org/papers/volume20/18-444/18-444.pdf) I could get an average improvement of 0.029 in AUC
for the classification datasets. Also not a very big difference, so this is not very surprising. 

## Further work

In my next blog entry I will talk a bit about **catboost** and **lightGBM**. 
Especially the first one promises to provide much better default performances than **xgboost** and I will compare it with the random forest 
implementation **ranger** and the support vector machine implementation **liquidSVM** in their default state. 

