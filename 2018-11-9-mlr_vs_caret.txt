%---
%title: "mlr vs. caret"
%layout: post
%output:
%  html_document: default
%  pdf_document: default
%comments: yes
%---

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

Both packages heavily depend on other packages and hence installation takes a long time. For caret it took me 925 seconds on my debian machine while for mlr it took XXX seconds without including suggested packages. 

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

Let's compare the different feature that the two packages have to offer for different topics. 

## Preprocessing

Both packages have several possibilities for preprocessing of the dataset. Both include standard procedures like dummy feature creation (e.g. for categorical predictors), normalization and imputation and include several feature selection methods. In caret also standard transformations (scaling, PCA, Box-Cox, Yeo-Johnson, etc.) can be used. An advantage of mlr is, that all preprocessing methods of caret can be used via a Wrapper that makes them accessible for mlr. In both packages these preprocessing steps can be used in the resampling process. 

## Tasks

mlr has more possibilities to create different tasks: Classification, Regression, Survival, Clustering, Multilabel, Cost-sensitive, Imbalanced data, Functional data, Spatial data. 

caret only seems to have possibilities for Classification, Regression and Cost-sensitive.

mlr is more flexible and richer here. 

## Learners

Currently 271 learners are supported in caret. For mlr it is ''only'' 165. I think bigger quantity is not very important here, as the most common and most important learners are implemented in both packages. What is clearly missing is a list of recommended learners in both packages. New users just have to look for the ''standard'' packages, without knowing if they are good or if there are possibly better implementations. mlr provides an easy way to look up the learners and their properties via ```listLearners()``` or via the [tutorial]((https://mlr.mlr-org.com/index.html)) in a table format. Learners for caret can be looked up in the tutorial, [chapter 7](http://topepo.github.io/caret/train-models-by-tag.html). 

Both packages provide the possibility to extend the learners. 

No clear winner here, although documentation seems to be better for mlr here.

## Resampling strategies

There are several resampling strategies available in both packages. Of course both include the most standard procedure (repeated) k-fold cross-validation. Moreover the strategies holdout, bootstrap and leave-one-out are available for both. Both have the option for growing window cross-validation which is especially useful for time series data. caret has additionally the possibility to use out-of-bag methods that are available for methods that use bagging like e.g. random forest. This is not available for mlr, only the out-of-bag predictions of a normally trained random forest model can be extracted here. 

Small advantage for caret. 

## Measures

In mlr there are currently [71 measures](https://mlr.mlr-org.com/articles/tutorial/measures.html) implemented for all the different tasks that can be handled in mlr. Custom measures can be added by the user (see [here](https://mlr.mlr-org.com/articles/tutorial/create_measure.html)). Only one measure is given as default (mse in regression and mmce in classification).

caret has two standard measures that are used for each of the tasks (RMSE and Rsquared for regression and Accuracy and Kappa for classification). They are changeable via the ```trainControl``` argument but it is more complicated to do that than in mlr, I would say. In total caret has also much less already implemented measures for regression and classification tasks. 

## Benchmarking

mlr provides a function for executing a benchmark of several learners and several datasets/tasks at once (also parallelizable). 
No such functionality is available in caret. 

## Tuning

Tuning is done internally and automatically in the caret package. What is tuned is described in the [learner section](http://topepo.github.io/caret/train-models-by-tag.html) of the tutorial. This can be changed by the user to grid search or random search.

In mlr tuning is not done automatically. There are functions and wrappers to perform the tuning. Tuning methods that are supported are random search, grid search, F-Racing and model-based optimization via the mlrMBO package. 

For both packages there are plots to visualize the tuning process. 

Light advantage for mlr here as it support advanced tuning algorithms like F-Racing and model-based optimization. 

## Visualization of results

Visualising your results and getting informations about your models is an important task in the modelling pipeline. Both packages provide a lot of possibilities here. For both packages we have ROC-Analysis and calibration curves. For mlr there are additionally partial dependence plots, learning curves, cut-off curves and residual plotting, while caret provides method specific variable importances and lift curves. 

# Speed

There shouldn't be a big time difference for the operations of the package itself as most of the operations are not computationally intensive. 

## Difference in the default mode

caret has prespecified tuning if the learners are used in their default version so it will in the default mode take more time than mlr for which tuning is not done in the default method. In the example above it lead to a runtime of 2.4 seconds for mlr and 3.2 seconds for caret. In general the automatic tuning will also lead to better performance. 

## Parallelization

Parallelization in the caret package only works internally when applying a tuning strategy like grid search by using the [doParallel](http://topepo.github.io/caret/parallel-processing.html) package which is based on [snow](https://cran.r-project.org/web/packages/snow/index.html). 

In contrast in mlr you have more options. You can parallelize on different [levels](https://mlr.mlr-org.com/articles/tutorial/parallelization.html): While using benchmark with different learners and datasets, when resampling, while selecting Features, while tuning and while training an ensemble. Parallelization can be done via [parallel](https://stat.ethz.ch/R-manual/R-devel/library/parallel/doc/parallel.pdf), [snow](https://cran.r-project.org/web/packages/snow/index.html) or via [batchtools](https://cran.r-project.org/web/packages/batchtools/batchtools.pdf). Especially useful is the function ```batchmark``` which is good for parallelizing benchmark via [batchtools](https://cran.r-project.org/web/packages/batchtools/batchtools.pdf). 

# Code writing

mlr seems to be easier extandable and configurable once you digged into it. 

# Overall result

## Overview in table

## Thanks
Thanks to Rahul Sangole, who helped me a bit with informations about the caret package as I was not very familiar with it. 

This blog post was originally posted in my [personal blog](http://philipppro.github.io/). 


## Installation

The installation of the R package can be done via `devtools::install_github("PhilippPro/tuneRanger")`

## Details

Here is a brief R-Code that shows how it works. We need also the [mlr](https://github.com/mlr-org/mlr) package to make it run. 
First a mlr task has to be created via `makeClassifTask` or `makeRegrTask`. After that the runtime of the tuning can be estimated 
with `estimateTuneRFTime`. 

```{r}
library(tuneRanger)
library(mlr)
# We make an mlr task with the iris dataset here 
iris.task = makeClassifTask(data = iris, target = "Species")
# Rough Estimation of the Tuning time
estimateTuneRangerTime(iris.task)
# Tuning process (takes around 1 minute); Tuning measure is the multiclass brier score
res = tuneRanger(iris.task, measure = list(multiclass.brier))
res
```

The execution of the tuning can be done with the `tuneRanger` function. The `task` has to be passed as well as 
a `measure` that should be tuned. Which measures are available can be looked up on the
[mlr tutorial](http://mlr-org.github.io/mlr-tutorial/release/html/measures/index.html) page. Typical measures in case of 
classification are the mean missclassification rate (also calles error rate), the AUC, the brier score and the 
logarithmic loss and the mean squared error in case of regression. 

All other parameters are well defined and do not have to be changed. `iters` specifies the number of iterations 
(for each iteration one random forest is trained and evaluated), the default of 100 provide good results in general. 
Moreover the number of threads (`num.threads`) can be set, that means how many CPUs should be used. Also the number of trees can be 
specified via `num.trees`. Other parameters of the underlying [ranger](https://github.com/imbs-hl/ranger) package can be fixed with
the `parameters` and `tune.parameters` arguments. 

The outcome is a recommendation vector for the hyperparameters and a model with the tuned hyperparameters. 

## How it works

The current algorithm works as follows:
*  Sequential model-based optimization is used as tuning strategy with 30 evaluated random points for the initial design and 
70 iterative steps in the optimization procedure. [mlrMBO](https://github.com/mlr-org/mlrMBO) is used internally for tuning. 
*  The three parameters min.node.size, sample.fraction and mtry are tuned at once. 
*  Out-of-bag predictions are used for evaluation, which makes it much faster than other packages and tuning strategies that 
use for example 5-fold cross-validation. 
*  Classification as well as regression is supported.
*  The default measure that is optimized is the brier score for classification and the mean squared error for regression. 

## Benchmark study

In a benchmark study I compared the algorithm with some other tuning implemenations for random forests in R and with a 
standard random forest without tuning. The competitors are the following:

* Different target performance measures for **tuneRanger**; the default is to tune the brier score for classification and mean squared error for regression. We also look at the versions that tune the AUC and the logarithmic loss in the case of classification.
* Two packages that already perform tuning for random forests:
  + [mlrHyperopt](https://github.com/jakob-r/mlrHyperopt) which uses also [mlrMBO](https://github.com/mlr-org/mlrMBO) in the 
  background and has predefined tuning parameters and tuning spaces for many supervised learning algorithms. We use the default.
  + [caret](https://github.com/topepo/caret) implementation of ranger which performs automatically the tuning of the `mtry` parameter.
* The standard random forest algorithm (from the **ranger** package), to see if we get better results than the default algorithm

Our benchmark study is conducted on several datasets from [OpenML](https://github.com/openml/OpenML). 
We use the [OpenML100](https://arxiv.org/abs/1708.03731) benchmarking suite and download it via the OpenML R package. 
For classification we only use datasets that have a binary target and no missing values. We classify the datasets into small, 
medium and big by executing **tuneRanger** on 10 cores and recording the runtime. If the runtime is less 
than 60 seconds, the dataset is classified as small, if it is between one minute and ten minutes as medium and datasets with a 
runtime bigger than 10 minutes are classified as big. 

For the small and medium datasets we perform a 5-fold cross-validation and repeat it 10 times. The average results for these 
30 datasets can be seen in the table below. The ending of **tuneRanger** specifies which measure was tuned. 


|                  | -- Error rate| -- (Multiclass) AUC| -- Brier Score| -- Logarithmic Loss| -- Training Runtime|
|:-----------------|-------------:|-------------------:|--------------:|-------------------:|-------------------:|
|tuneRangerMMCE    |        0.0988|              0.9060|         0.1445|              0.2464|            193.5932|
|tuneRangerAUC     |        0.0991|              0.9088|         0.1456|              0.2483|            187.7843|
|tuneRangerBrier   |        0.0991|              0.9069|         0.1398|              0.2351|            183.6576|
|tuneRangerLogloss |        0.0995|              0.9073|         0.1398|              0.2338|            178.1290|
|hyperopt          |        0.0979|              0.9064|         0.1440|              0.2484|            317.3986|
|caret             |        0.1039|              0.9064|         0.1515|              0.2548|            168.3151|
|ranger            |        0.1074|              0.9041|         0.1632|              0.2747|              3.9578|

The average rank of all datasets can be seen in following table:

|                  | -- Error rate| -- (Multiclass) AUC| -- Brier Score| -- Logarithmic Loss| -- Training Runtime|
|:-----------------|-------------:|-------------------:|--------------:|-------------------:|-------------------:|
|tuneRangerMMCE    |          4.28|                3.43|           4.53|                4.48|                5.40|
|tuneRangerAUC     |          3.98|                5.47|           4.50|                4.30|                4.73|
|tuneRangerBrier   |          2.92|                4.53|           1.60|                2.40|                4.97|
|tuneRangerLogloss |          3.33|                4.67|           2.17|                2.20|                4.17|
|hyperopt          |          3.18|                3.27|           4.33|                5.05|                4.90|
|caret             |          4.93|                3.42|           5.55|                4.85|                2.83|
|ranger            |          5.37|                3.22|           5.32|                4.72|                1.00|


We see that on average the **tuneRanger** methods outperform the **ranger** package and the **caret** package for all measures.
Also tuning the specific measure does on average always provide the best results among all classifiers. This is also true 
for the error rate where **mlrHyperopt** performs best, because **mlrHyperopt** tunes the error rate internally. 
It is only partly true if we look at the ranks. **tuneRangerBrier** has the best average rank for the mmce, not **tuneRangerMMCE**. 

**caret** is on average better than ranger, but is clearly outperformed by the **tuneRanger** methods, showing that tuning only 
the `mtry` parameter is not enough. 

**mlrHyperopt** is quite competitive. This is not surprising as it also uses **mlrMBO** for tuning like **tuneRanger**. The 
main disadvantage is regarding the runtime. On average it takes longer, as it does not use the out-of-bag method for evaluation 
like **tuneRanger** but 5-fold cross-validation, which takes 5 times longer. This does not play a role for smaller datasets 
as the execution of the tuning can be done in less than ten minutes, but plays an important role for bigger datasets. 

Below we see a graph that shows the average runtime for the algorithms.

![graphic](/images/rf_tune_time.png "graphic")

We see that for smaller datasets the runtime of **mlrHyperopt** is smaller than **tuneRanger**, but when runtime increases 
it gets worse and worse compared with the **tuneRanger** algorithm. Because of this I think that **tuneRanger** is 
preferable especially for bigger datasets, when runtime also plays a more important role. 

In a following blog post I will post results for bigger datasets. 
