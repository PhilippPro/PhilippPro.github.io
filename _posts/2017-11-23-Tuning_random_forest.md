---
title: "Tuning random forest"
layout: post
output:
  html_document: default
  pdf_document: default
comments: yes
---

Random forest is one of the standard approaches for supervised learning nowadays. One of its advantages is that it does 
not require tuning of the hyperparameters to perform good. But is that really true? Maybe we are not only interested in a 
good model but in the best model we could get...

Tuning requires a lot of time and computational effort and is still difficult to execute for beginners as there are no 
standardized ways and only few packages for tuning. With my new package [tuneRanger](https://github.com/PhilippPro/tuneRanger) 
I try to fill the gap for the random forest algorithm in R. 

<!--excerpt-->

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
