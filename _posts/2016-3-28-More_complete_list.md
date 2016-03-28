---
layout: post
title: A More Complete List of Random Forest R Packages
comments: True
---

In my last post I provided a small list of some R packages for random forest. Today I will provide a more complete list of random forest R packages. In the first table I list the R packages which contains the possibility to perform the standard random forest like described in the original Breiman paper. 

|**package**     | **RStudio downloads in the last month**|
|:---------------|---------------------------------------:|
|randomForest    |                                   28353|
|xgboost         |                                    4537|
|randomForestSRC |                                    2291|
|ranger          |                                    1347|
|Rborist         |                                     284|

<!--excerpt-->

**xgboost** is a package that is used for boosted tree algorithms and is the current *state of the art* for machine learning challenges for example on the platform Kaggle due to its flexibility and very good performance. **xgboost** also contains the possibility to grow a random forest, as can be seen in the last section of this [tutorial page](https://cran.r-project.org/web/packages/xgboost/vignettes/discoverYourData.html).

A graphical representation of this table:

![graphic](/images/rfpack.png "graphic")

Two algorithms that are especially constructed for big datasets are the following three (**ranger** and **Rborist** could also have been listed here):

|**package**        | **RStudio downloads in the last month**|
|:--------------|---------------------------------------:|
|h2o            |                                    3397|
|ParallelForest |                                     261|
|bigrf          |                                       6|

**bigrf** is currently not on **CRAN** anymore [as check problems were not corrected despite reminders,](https://cran.r-project.org/web/packages/bigrf/index.html) that is the reason for the low download statistic. 

An interesting blog post from Szilard about benchmarking different packages (also on other software like Python and **only** one dataset) on big datasets and classification tasks can be found [here](http://datascience.la/benchmarking-random-forest-implementations/). It is mainly interesting for seeing the differences in speed performance. Benchmarking performance on only one dataset is clearly not enough. 
The github page of the whole study can be seen [here](https://github.com/szilard/benchm-ml)

Many more packages exist which are slightly different from the original random forest algorithm. These are listed in the following table:

|**package**             | **RStudio downloads in the last month**|
|:-------------------|---------------------------------------:|
|rpart               |                                   21585|
|party               |                                   14338|
|extraTrees          |                                    1408|
|RRF                 |                                     516|
|rotationForest      |                                     437|
|rFerns              |                                     431|
|obliqueRF           |                                     252|
|wsrf                |                                     232|
|randomUniformForest |                                     189|
|trimTrees           |                                     134|
|roughrf             |                                     126|
12:  randomForestSRC(Syn) | 2291

**rpart** is just for creating a single tree. **party** contains **cforest** which are random forests based on conditional inference trees. 

R-code for obtaining the tables and the graphic:

```
library(cranlogs)
library(data.table)
library(knitr)

downloads = cran_downloads(packages = c("randomForest", "xgboost", "randomForestSRC", "ranger", "Rborist"), when = c("last-month"))
downloads = data.table(downloads)
downloads = downloads[,sum(count), by = "package"]
colnames(downloads) = c("**package**", "**RStudio downloads in the last month**")
kable(downloads, format = "markdown")
barplot(downloads[,2], names.arg = downloads$package, col = "blue")

downloads = cran_downloads(packages = c("h2o","ParallelForest", "bigrf"), when = c("last-month"))
downloads = data.table(downloads)
downloads = downloads[,sum(count), by = "package"]
colnames(downloads) = c("**package**", "**RStudio downloads in the last month**")
kable(downloads, format = "markdown")

downloads = cran_downloads(packages = c("rpart", "RRF", "randomForestSRCSyn", "obliqueRF", "rotationForest", 
                                        "rFerns", "randomUniformForest", "wsrf", "roughrf", "trimTrees", "extraTrees", "party" ), when = c("last-month"))
downloads = data.table(downloads)
downloads = downloads[,sum(count), by = "package"]
downloads = downloads[order(downloads$V1, decreasing = T),]
colnames(downloads) = c("**package**", "**RStudio downloads in the last month**")
kable(downloads, format = "markdown")
```
