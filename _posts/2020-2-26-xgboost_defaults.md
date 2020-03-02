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

Hence, I wanted to use the data used in the paper to set *nrounds* to 500 and optimize the other 
parameters to get optimal defaults. 

<!--excerpt-->

I used the same method as mentioned in the paper with the 38 classification datasets and obtained the following (rounded) defaults:

| **nrounds**|       **-----eta**| **--subsample**|**--booster**| **--max_depth**| **--min_child_weight**|
|-----------:|------------------:|---------------:|:-------------|----------------:|----------------------:|
|         500|              0.052|            0.87|        gbtree|               11|                      2|

| **--colsample_bytree**| **--colsample_bylevel**|
|----------------------:|-----------------------:|
|                   0.71|                    0.64|

### Performance on 29 regression datasets

Now I wanted to know how well this defaults perform on other tasks. So I took a collection of 29 regression tasks of different 
domains, that I collected from different sources such as [OpenML](https://www.openml.org/) and compared my 
defaults against the defaults of **xgboost** with **nrounds**=500. Code can be seen [here](https://github.com/PhilippPro/OpenML-bench-regr/blob/master/R/benchmark_xgboost_500.R).

The results were not really mindblowing. Here you can see performance graphs for two different measures (R-Squared and Spearman's Rho) 
using 10 times repeated 5-fold CV:

![graphic](/images/xgboost_500_rsq_results.png "graphic")

![graphic](/images/xgboost_500_spearman_results.png "graphic")

It is visible that my defaults perform better than the package defaults in most of the cases regarding the R$^2$ and also regarding 
Spearman's Rho (a nonparametric measure). 

The average values can be seen in the following table:

|             |--xgboost_old_default|--xgboost_my_default|
|:------------|--------------------:|-------------------:|
|R-squared    |                0.610|               0.645|
|Spearman Rho |                0.775|               0.785|

The percentage of datasets where my new defaults are better than the old defaults is 66% for R$^2$ and 75% for Spearman's Rho. 

So all in all my new defaults do not provide a very big performance gain (on average 0.03 or R$^2$), but seem to be better 
than the package defaults. 

In my [paper](http://www.jmlr.org/papers/volume20/18-444/18-444.pdf) I could get an average improvement of 0.029 in AUC
for the classification datasets with my defaults. Also not a very big difference, so this is not very surprising. 

### Runtime

The runtime was a bit longer with my defaults, but not really much longer. You can see the runtime below:

![graphic](/images/xgboost_500_time_results.png "graphic")

The longer runtime is mainly due to the *max_depth* parameter, which I set bigger (11 instead of 6).

All in all the datasets that I used were rather small, so bigger datasets would provide more interesting results. 


## Further work

In my next blog entry I will talk a bit about **catboost** and **lightGBM**. 
Especially the first one promises to provide much better default performances than **xgboost** and I will compare it with the random forest 
implementation **ranger** and the support vector machine implementation **liquidSVM** in their default state. 

## PS:

I thought again about the results. Some conclusions regarding the parameters:

- In the section with low R-squared the default of xgboost performs much worse than my defaults. These are datasets that are hard to fit and few things can be learned. The higher eta (eta=0.1) leads to too much overfitting compared to my defaults (eta=0.05). If you would set nrounds lower than 500 the effect would be smaller. You could leave eta=0.1 but set nrounds to a lower default value. 
- I am not sure how big is the effect for max_depth. In my [paper](http://www.jmlr.org/papers/volume20/18-444/18-444.pdf) the effect was quite small, so you could leave it on 5 to get better runtimes.
- The subsample and colsample parameters could be safely set to values between 0.6 and 0.8 to get better results.
