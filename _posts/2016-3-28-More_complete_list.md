---
layout: post
title: A More Complete List of Random Forest R Packages
comments: True
---

In my last post I provided a small list of some R packages for random forest. Today I will provide a more complete list of random forest R packages. In the first table I list the R packages which contain the possibility to perform the standard random forest like described in the original Breiman paper. 

 **package**              |  **RStudio downloads in the last month**
 -------------        |  -------------:
1:    randomForest |  28353
2:         xgboost |  4537
3: randomForestSRC |  2291
4:          ranger |  1347
5:         Rborist |  284

**xgboost** is a package that is used for boosted tree algorithms and is the current *state of the art* for machine learning challenges for example on the platform Kaggle due to its flexibility and very good performance. **xgboost** also contains the possibility to grow a random forest, as can be seen in the last section of this [tutorial page](https://cran.r-project.org/web/packages/xgboost/vignettes/discoverYourData.html).

Two algorithms that are especially constructed for big datasets are the following three (**ranger** and **Rborist** could also have been listed here):

 **package**              |  **RStudio downloads in the last month**
 -------------        |  -------------:
1:            h2o | 3397
2: ParallelForest | 261
3:          bigrf | 6

**bigrf** is currently not on **CRAN** anymore [as check problems were not corrected despite reminders,](https://cran.r-project.org/web/packages/bigrf/index.html) that is the reason for the low download statistic. 

An interesting blog post about benchmarking different packages (also on other software like Python and **only** one dataset) on big datasets and classification tasks can be found [here](http://datascience.la/benchmarking-random-forest-implementations/). It is mainly interesting for seeing the differences in speed performance. Benchmarking on only one dataset is clearly not enough. 
The github page of the whole study can be seen [here](https://github.com/szilard/benchm-ml)

Many more packages exist which are slightly different from the original random forest algorithm. These are listed in the following table:

 **package**              |  **RStudio downloads in the last month**
 1:               rpart | 21585
 2:               party | 14338
 3:          extraTrees | 1408
 4:                 RRF | 516
 5:      rotationForest | 437
 6:              rFerns | 431
 7:           obliqueRF | 252
 8:                wsrf | 232
 9: randomUniformForest | 189
10:           trimTrees | 134
11:             roughrf | 126
12:  randomForestSRC(Syn) | 2291

**rpart** is just for creating a single tree. **party** contain **cforest** which are random forests based on conditional inference trees. 


Graphic

R-code for obtaining the tables and the graphic:


