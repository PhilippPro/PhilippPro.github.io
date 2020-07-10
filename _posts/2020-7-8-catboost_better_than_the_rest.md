---
title: "Is catboost the best gradient boosting R package?"
layout: post
output:
  html_document: default
  pdf_document: default
comments: yes
---

Several R packages that use different methods are out there for using gradient boosting methods. 
The three most famous ones are currently **xgboost**, **catboost** and **lightgbm**. 
I want to compare these three to find out which is the best one in their default mode without tuning. 
These algorithms are not pure gradient boosting algorithms but combine it with other useful methods such as bagging which is for example used in random forest.

<!--excerpt-->

## Algorithms and Installation (for R)

- **xgboost**: The installation is very easy. You can install it via the function *install.packages*, 
because it is available on CRAN. 
- **catboost**: It is also easy to install. You need to install **devtools** and you can then 
follow the instructions on their documentation [page](https://catboost.ai/docs/installation/r-installation-binary-installation.html).
- **lightgbm**: The installation is a bit complicated, because you need to install several other packages and programs beforehand.
The instructions can be found [here](https://github.com/Microsoft/LightGBM/tree/master/R-package#installation).
- As a baseline algorithm I used the random forest implemenation **ranger**. This algorithm performs very well with default parameter settings. I use 500 trees in each random forest, all the other settings are set to their default values. 

## Algorithm versions

The algorithms are used with their most actual version on first of March 2020. **checkpoint** R package is used to obtain the correct versions on this date for the packages available on CRAN. For the other I took the version on github available on this date. 

## Why using the defaults?

To be honest, I am a rather lazy guy. I do not want to do a lot of manual tuning and modern algorithms should provide automatic tuning. We are in the era of self-driving cars but should still tune our algorithms by hand? I am aware, that especially in gradient boosting tuning provides performance improvements, but I will not do the tuning in this benchmark here. 

## Parameter settings

I try to use the algorithms with as less adjustments as possible. I had a quick look on their documentation page and set the parameters as follows:

- **catboost** is used with its package default settings
- On the **lightgbm** page it is recommended to use the following settings, which I use: nrounds=500, learning_rate=0.1, num_threads=5
- For xgboost I use their default setting with 500 rounds (**xgboost_def**)
- Moreover I set up a second xgboost version (**xgboost_best**) with the best parameter settings that I obtained in one of my [publications](http://www.jmlr.org/papers/volume20/18-444/18-444.pdf). These are: nrounds=500, eta=0.0518715, subsample=0.8734055, booster="gbtree", max_depth=11, min_child_weight=1.750185, colsample_bytree=0.7126651, colsample_bylevel=0.6375492.

## Multi threading

All algorithms provide parallel execution on several CPU threads. I used 5 threads for each algorithm. 

## Adjustments for categorical data

Unfortunately **xgboost** does not provide an automatic method for categorical input data. I implemented a manual method by which categorical variables were automatically converted to dummy variables. 
**catboost** and **lightgbm** do provide methods for categorical input data. 

## Datasets

I took a collection of 31 regression datasets of different domains, that I collected from different sources such as [OpenML](https://www.openml.org/). 
The datasets are rather small. They can be found via OpenML and the tag *"OpenML-Reg19"*:

```{r}
library(OpenML)
tasks = listOMLTasks(tag = "OpenML-Reg19")
tasks = tasks[!(tasks$name %in% c("aloi", "BNG(satellite_image)", "black_friday")),]  
```

I excluded the datasets "aloi", "BNG(satellite_image)" and "black_friday", because calculation took very long time or they provided errors on some of the algorithms. 

## Evaluation

I used 10-times repeated 5-fold crossvalidation for each of the algorithms to estimate the performance of the algorithms on the datasets. I use the measures R-Squared and the non-parametric measure Spearmans-Rho, which only evaluates, if the observations are predicted in the correct order. 

## Code

The code is available on this github page: [OpenML-bench](https://github.com/PhilippPro/OpenML-bench/blob/master/R/main.R).

## Results

The results for R-Squared can be seen here:

![graphic](/images/rsq_results.png "graphic")

Of the three gradient boosting algorithms **catboost** performs best in general and is outperformed only in very few cases by the other algorithms. The **xgboost_best** version of **xgboost** usually provides better results than the default parameter settings (**xgboost_def**). 
In some cases, especially when R-Squared is negative (which means that the algorithm is worse than simply taking the average), random forest (**ranger**) performs better. Possibly on these datasets outliers lead to the worse performance, but this could be examined further. 

The results for Spearmans-Rho are similar:

![graphic](/images/spearman_results.png "graphic")

All algorithms are above 0 for each dataset, which means that they provide better ordering than random predictions. On most datasets **catboost** provides the best results. On some datasets **lightgbm** provides bad results compared to the other algorithms. 

## Calculation time

In the following we can see the average calculation time results:

![graphic](/images/time_results.png "graphic")

The datasets are rather small, so the calculation time is also rather small (below 30 seconds on 5 CPU threads). **catboost** and **xgboost_best** have longest runtimes **lightgbm** is the fastest. 

## Further work

Results for classification will follow in a future blog post. If you have comments to the analysis, better defaults of the packages or other comparable packages, let me know. Some of the algorithms are still under development, so results may change. 

## Annex

I also examined **liquidSVM**, but it provided bad results on several datasets, which was different from results I obtained some months before. So I excluded it from the analysis for the moment. 



