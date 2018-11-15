---
title: "mlr vs. caret"
layout: post
output:
  html_document: default
  pdf_document: default
comments: yes
---

Let's compare the two popular R packages for machine learning [mlr](https://github.com/mlr-org/mlr) and [caret](https://github.com/topepo/caret).

caret is longer on the market, its first CRAN release seems to be from 2007, while mlr came to CRAN on 2013. As for now, caret seems to be more popular, according to [cranlogs](https://cran.r-project.org/web/packages/cranlogs/index.html) caret was downloaded 178029 times in the last 30 days, while mlr was downloaded 11408 times in the last 28 days. 

<!--excerpt-->

The purpose of the packages seems to be quite similar. The caret website defines caret as follows:

''The caret package (short for _C_lassification _A_nd _RE_gression _T_raining) is a set of functions that attempt to streamline the process for creating predictive models.''

The purpose of mlr is described on their website:

''R does not define a standardized interface for all its machine learning algorithms. Therefore, for any non-trivial experiments, you need to write lengthy, tedious and error-prone wrappers to call the different algorithms and unify their respective output. [...] The framework provides supervised methods like classification, regression and survival analysis along with their corresponding evaluation and optimization methods, as well as unsupervised methods like clustering. It is written in a way that you can extend it yourself or deviate from the implemented convenience methods and construct your own complex experiments or algorithms.''

Both packages want to automatize standard processes for standard tasks like classification and regression and make life easier for data scientists. 
So which package to use? 
I will try to do a fair comparison of these two packages. As I contributed to the mlr package I may be biased, but I will try to do it as fair as possible. 

# Installation

Both packages heavily depend on other packages and hence installation takes a long time. I tested both packages on my windows machine with a fresh R version and without having installed any dependencies beforehand. For caret it took me 140 seconds while for mlr it took 46 seconds without including suggested packages. 

# Documentation

Both have an online tutorial. [caret's one](http://topepo.github.io/caret/index.html) is good for learning from scratch, but for looking things up quickly it is rather unpractical. Here lies the strength of the [mlr tutorial](https://mlr.mlr-org.com/index.html).

caret provides more things. There is the [Applied Predictive Modelling book](https://www.springer.com/de/book/9781461468486) which tries to ''cover the overall predictive modeling process.'' It is not freely available online, like e.g. some books of Hadley Wickham (e.g., [Advanced R](http://adv-r.had.co.nz/), [R for Data Science](https://r4ds.had.co.nz/) or [R packages](http://r-pkgs.had.co.nz/)) or [The elements of statistical learning](https://web.stanford.edu/~hastie/Papers/ESLII.pdf).

Moreover there is a [datacamp course](https://www.datacamp.com/courses/machine-learning-toolbox) that uses caret. 

Cheatsheets are available for both. The one for [mlr](https://github.com/rstudio/cheatsheets/raw/master/mlr.pdf) seems to be more extensive and detailed than the [caret](https://github.com/rstudio/cheatsheets/raw/master/caret.pdf) one. 

All in all, especially because of the nice book, caret seems to be the winner here. 

# Simple code example

To compare the two packages directly here you can see the standard codes of lines that are necessary to perform a simple 5-fold cross-validation for the boston housing dataset and random forest package ranger.

For mlr:

```{r}
data(BostonHousing, package = "mlbench")
regr.task = makeRegrTask(data = BostonHousing, target = "medv")
lrn = makeLearner("regr.ranger")
rdesc = makeResampleDesc("CV", iters = 5)
res = resample(learner = lrn, task = regr.task, resampling = rdesc, measure = list(RMSE))
res
```

For caret:

```{r}
data(BostonHousing, package = "mlbench")
fitControl = trainControl(method = "cv", number = 5)
fit = train(medv ~ ., data = BostonHousing, method = "ranger", trControl = fitControl)
fit
```

In this code you can already see a crucial difference. In mlr you have to create a task and a learner. This means more code writing, but gives the user also more flexibility and overview of what he is doing. In caret you can give the data and the method directly to the ```train``` function and internally some tuning is done for the user without having the possibility to adjust this easily. 

Things can be changed in both packages (e.g., by giving directly the character ```"regr.ranger"``` as learner in ```train``` or by creating your own learner in caret like described [here](http://topepo.github.io/caret/using-your-own-model-in-train.html)) but these are the standard ways of doing things in the two packages. 

No clear winner here. 

# Features

Let's compare the different feature that the two packages have to offer for different issues of the machine learning process. 

## Preprocessing

Both packages have several possibilities for preprocessing of the dataset. Both include standard procedures like dummy feature creation (e.g. for categorical predictors), normalization and imputation and include several feature selection methods. In caret also standard transformations (scaling, PCA, Box-Cox, Yeo-Johnson, etc.) can be used. An advantage of mlr is, that all preprocessing methods of caret can be used via a Wrapper that makes them accessible for mlr. In both packages these preprocessing steps can be used in the resampling process. 

No clear winner here. 

## Tasks

mlr has more possibilities to create different tasks: Classification, Regression, Survival, Clustering, Multilabel, Cost-sensitive, Imbalanced data, Functional data, Spatial data. 

caret only seems to have possibilities for Classification, Regression and Cost-sensitive.

mlr is better here.

## Learners

Currently 271 learners are supported in caret. For mlr it is ''only'' 165. I think bigger quantity is not very important here, as the most common and most important learners are implemented in both packages. What is clearly missing is a list of recommended learners in both packages. New users just have to look for the ''standard'' packages, without knowing if they are good or if there are possibly better implementations. mlr provides an easy way to look up the learners and their properties via ```listLearners()``` or via the [tutorial]((https://mlr.mlr-org.com/index.html)) in a table format. Learners for caret can be looked up in the tutorial, [chapter 7](http://topepo.github.io/caret/train-models-by-tag.html). 
Both packages provide the possibility to extend the learners. 

No clear winner here, although documentation seems to be better for mlr here.

## Resampling strategies

There are several resampling strategies available in both packages. Of course both include the most standard procedure (repeated) k-fold cross-validation. Moreover the strategies holdout, bootstrap and leave-one-out are available for both. Both have the option for growing window cross-validation which is especially useful for time series data. caret has additionally the possibility to use out-of-bag methods that are available for methods that use bagging like e.g. random forest. This is not available for mlr, only the out-of-bag predictions of a normally trained random forest model can be extracted here. 

Small advantage for caret. 

## Measures

In mlr there are currently [71 measures](https://mlr.mlr-org.com/articles/tutorial/measures.html) implemented for all the different tasks that can be handled in mlr. Custom measures can be added by the user (see [here](https://mlr.mlr-org.com/articles/tutorial/create_measure.html)). Only one measure is given as default (mse in regression and mmce in classification).

caret has two standard measures that are used for each of the tasks (RMSE and Rsquared for regression and Accuracy and Kappa for classification). They are changeable via the ```trainControl``` argument but it is more complicated to do that than in mlr, I would say. 

mlr wins here.

## Benchmarking

mlr provides a function for executing a benchmark of several learners and several datasets/tasks at once (also parallelizable). No such functionality is available in caret. 

## Tuning

Tuning is done internally and automatically in the caret package. What is tuned is described in the [learner section](http://topepo.github.io/caret/train-models-by-tag.html) of the tutorial. This can be changed by the user to grid search or random search.

In mlr tuning is not done automatically. There are functions and wrappers to perform the tuning. Tuning methods that are supported are random search, grid search, F-Racing and model-based optimization via the mlrMBO package. 

For both packages there are plots to visualize the tuning process. 

Light advantage for mlr here as it support advanced tuning algorithms like F-Racing and model-based optimization. 

## Visualization of results

Visualising your results and getting informations about your models is an important task in the modelling pipeline. Both packages provide a lot of possibilities here. For both packages we have ROC-Analysis and calibration curves. For mlr there are additionally partial dependence plots, learning curves, cut-off curves and residual plotting, while caret provides method specific variable importances and lift curves. 

No clear winner here. 

# Speed

There shouldn't be a big time difference for the operations of the package itself as most of the operations are not computationally intensive. 

## Difference in the default mode

caret has prespecified tuning if the learners are used in their default version so it will in the default mode take more time than mlr for which tuning is not done in the default method. In the example above it lead to a runtime of 2.4 seconds for mlr and 3.2 seconds for caret. In general the automatic tuning will also lead to better performance. 

No clear winner here. 

## Parallelization

Parallelization in the caret package only works internally when applying a tuning strategy like grid search by using the [doParallel](http://topepo.github.io/caret/parallel-processing.html) package which is based on [snow](https://cran.r-project.org/web/packages/snow/index.html). 

In contrast in mlr you have more options. You can parallelize on different [levels](https://mlr.mlr-org.com/articles/tutorial/parallelization.html): While using benchmark with different learners and datasets, when resampling, while selecting Features, while tuning and while training an ensemble. Parallelization can be done via [parallelMap](https://cran.r-project.org/web/packages/parallelMap/index.html) which works with all major parallelization backends: [parallel](https://stat.ethz.ch/R-manual/R-devel/library/parallel/doc/parallel.pdf), [snow](https://cran.r-project.org/web/packages/snow/index.html) or via [batchtools](https://cran.r-project.org/web/packages/batchtools/batchtools.pdf). Especially useful is the function ```batchmark``` which is good for parallelizing benchmarks via [batchtools](https://cran.r-project.org/web/packages/batchtools/batchtools.pdf). 

mlr has more possibilities here.

# Objects and configurability

In mlr you have an object for every part of the process: a task, a learner, a measure, a resample object. In caret you have the ```trainControl``` object which is configurable, but I would say that mlr is more intuitive and clear here. 
Because of this it is also easier in mlr to integrate new custom learners, measures, filters and imputation methods. 

mlr wins here.

# Further development

Caret will be retired. New developments include the packages recipes, yardstick, infer, parsnip and are all part of tidymodels. 

mlr developers are currently working on [mlr3](https://github.com/mlr-org/mlr3) which aims at being even more extensible and using R6, data.table and other useful packages that were not used by mlr. 

# Overall result

Counting together all categories mlr wins by 12:7. 

## Overview in table

## Thanks
Thanks to Rahul Sangole, who helped me a bit with informations about the caret package as I was not very familiar with it. 

This blog post was originally posted in my [personal blog](http://philipppro.github.io/). 
