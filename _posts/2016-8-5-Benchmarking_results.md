---
layout: post
title: Benchmark results (mlr-learners on OpenML)
comments: True
---

There are already some benchmarking studies about different classification algorithms out there. The probably most well known and 
most extensive one is the 
[Do we Need Hundreds of Classifers to Solve Real World Classication Problems?](http://www.jmlr.org/papers/volume15/delgado14a/source/delgado14a.pdf)
Paper. They use different software and also different tuning processes to compare 121 learners on more than 121 datasets, mainly 
from the [UCI](https://archive.ics.uci.edu/ml/datasets.html) site. They exclude different datasets, because their dimension 
(number of observations or number of features) are too high, they are not in a proper format or because of other reasons. 
There are also summarized some criticism about the representability of the datasets and the generability of benchmarking results. 
It remains a bit unclear if their tuning process is done also on the test data or only on the training data (page 3154). 
They reported the random forest algorithms algorithms to be the best one (in general) for multiclass classification datasets and 
the support vector machine (svm) the second best one. On binary class classification tasks neural networks also perform 
competitively. They recommend the R library **caret** for choosing a classifier. 

Other benchmarking studies use much less datasets and are much less extensive (e.g. the 
[Caruana Paper](https://www.cs.cornell.edu/~caruana/ctp/ct.papers/caruana.icml06.pdf)). Computational power was also not the same 
on these days. 

In my first approach for benchmarking different learners I will follow a more standardized approach, that can be easily 
redone in future when new learners or datasets are added to the analysis. 
I use the R package **OpenML** for getting access to OpenML datasets and the R package **mlr** (similar to caret, but more extensive)
to have a standardized interface to machine learning algorithms. Furthermore the experiments are done with the help of the 
R package [**batchtools**](https://github.com/mllg/batchtools), 
in order to parallelize the experiments (Installation via **devtools** package: devtools::install_github("mlr-org/mlr")).

<!--excerpt-->

The first step was to choose some datasets of the OpenML dataplatform. This was done in the [datasets.R](https://github.com/PhilippPro/benchmark-mlr-openml/blob/master/code/datasets.R)
file. I want to evaluate classification learners as well as regression learners, so I downloaded datasets for both tasks. 
The download date was 28.01.2016 so probably nowadays there are more available. I applied several exclusion criteria:

1. No datasets with missing values (this can be omitted in future with some imputation technique)
2. Each dataset only once (although there exist several tasks for some)
3. Exclusion of datasets that were obviously artificially created (e.g. some artificial datasets created by Friedman)
4. Only datasets with number of observations and number of features smaller than 1000 (this is done to get a first fast analysis; 
bigger datasets are added later)
5. In the classification case: only datasets where the target is a factor

Of course this exclusion criteria change the representativeness of the datasets. 

The benchmark file can be found [here](https://github.com/PhilippPro/benchmark-mlr-openml/blob/master/code/benchmark.R).
For the classification datasets all available classification learners in mlr, that 
can handle multiclass problems, provide probability estimations and can handle factor features, are used. "boosting" of the 
**adabag** package is excluded, because it took too long on our test dataset. 

For the regression datasets only regression learners that can handle factor features are included. 
The learners "btgp", "btgpllm" and "btlm" are excluded, because their training time was too long. 

In this preliminary study all learners were used with their default hyperparameter settings without tuning. 
The evaluation technique was 10-fold crossvalidation, 10 times repeated and it was executed by the resample function 
in mlr. The folds were the same for all the learners. The evaluation measures were the accuracy, the balanced error rate,
the (multiclass) auc, the (multiclass) brier score and the logarithmic loss for the classification and
the mean square error, mean of absolute error, median of square error and median of absolute error. Additionally the 
training time was recorded. 

On 12 cores it took me around 4 days for all datasets. 

I evaluated the results with help of the **data.table** package, which is good for handling big datasets and fast calculation 
of subset statistics. Graphics were produced with help of the **ggplot** package. 

For comparison, the learners were ranked on each dataset. (see [benchmark_analysis.R](https://github.com/PhilippPro/benchmark-mlr-openml/blob/master/code/benchmark_analysis.R))
There were a few datasets where some of the learners provided errors. 
In the first approach these were treated as having the worst performance and so all learners providing errors got the worst rank. 
If there were several learners they got all the *averaged* worst rank. 
The results are summarized in the following graphic:

![graphic](/images/1_best_algo_classif_with_na_rank.png "graphic")