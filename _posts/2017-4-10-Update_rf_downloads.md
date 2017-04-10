---
layout: post
title: Update on Random Forest Package Downloads
comments: True
---

I just updated the code from a [previous post](http://philipppro.github.io/More_complete_list/) where I 
analysed the download statistics of different random forest packages in R, see the code at the bottom of 
the article.

## Standard random forest

The download statistics of the random forest in march 2016 and in march 2017: 

|**package**      |  **--march 2016**|  **--march 2017**|  **--ratio of 2017/2016**|
|:----------------|-----------------:|-----------------:|-------------------------:|
|randomForest     |             29360|             55415|                      1.89|
|xgboost          |              4929|             12629|                      2.56|
|randomForestSRC  |              2559|              6106|                      2.39|
|ranger           |              1482|              5622|                      3.79|
|Rborist          |               298|               441|                      1.48|

What is clearly visible is the general increase in downloads of all packages that contain the standard random 
forest. The biggest gain in popularity could achieve the **ranger** package that allows to run the random forest in 
parallel on a simple machine. **xgboost** and **randomForestSRC** got a bigger increase than the standard 
**randomForest** package.

<!--excerpt-->

## Random forest for big datasets

The results of the random forest packages for big datasets:

|**package**     |  **--march 2016**|  **--march 2017**|  **--ratio of 2017/2016**|
|:---------------|-----------------:|-----------------:|-------------------------:|
|h2o             |              3719|              9666|                      2.60|
|ParallelForest  |               281|               279|                      0.99|
|bigrf           |                10|                 3|                      0.30|

**h2o** could achieve the biggest gain in cran downloads. The other two packages remain rather unknown 
to most of the R users.

## Packages with algorithms similar to random forest

Lastly the results for the other R packages that do not contain the standard random forest package but 
a similar version:

|**package**          |  **--march 2016**|  **--march 2017**|  **--ratio of 2017/2016**|
|:--------------------|-----------------:|-----------------:|-------------------------:|
|rpart                |             22769|             30552|                      1.34|
|party                |             15423|             32888|                      2.13|
|extraTrees           |              1446|              1112|                      0.77|
|RRF                  |               525|              1153|                      2.20|
|rFerns               |               450|               488|                      1.08|
|rotationForest       |               407|               342|                      0.84|
|obliqueRF            |               267|               255|                      0.96|
|wsrf                 |               248|               435|                      1.75|
|randomUniformForest  |               198|               179|                      0.90|
|trimTrees            |               148|                96|                      0.65|
|roughrf              |               137|                94|                      0.69|

**party** and **RRF** could gain the maximum increase in downloads. The simple tree package **rpart** 
remains with high download statistics but cannot gain a much bigger increase. **extraTrees** dropped a bit 
in the download statistic and does not gain so much attendance as last year. 

## Conclusion

**ranger**, **xgboost** and **party** are the biggest winner in the last year. 
Generally the downloads have approximately doubled - which indicates that random forest is getting 
more and more popularity in the R community. 

## R-code for obtaining the tables:
```
library(cranlogs)
library(data.table)
library(knitr)

downloads = cran_downloads(packages = c("randomForest", "xgboost", "randomForestSRC", "ranger", "Rborist"), from = "2016-03-01", to = "2016-03-31" )
downloads = data.table(downloads)
downloads = downloads[, sum(count), by = "package"]
downloads_new = cran_downloads(packages = c("randomForest", "xgboost", "randomForestSRC", "ranger", "Rborist"), from = "2017-03-01", to = "2017-03-31" )
downloads_new = data.table(downloads_new)
downloads_new = downloads_new[, sum(count), by = "package"]
downloads = cbind(downloads, downloads_new$V1, round(downloads_new$V1/downloads$V1, 2))
colnames(downloads) = c("**package**", "**--march 2016**", "**--march 2017**", "**--ratio of 2017/2016**")

kable(downloads, format = "markdown", padding = 2)
barplot(unlist(downloads[,2]), names.arg = unlist(downloads[,1]), col = "blue")

downloads = cran_downloads(packages = c("h2o","ParallelForest", "bigrf"), from = "2016-03-01", to = "2016-03-31" )
downloads = data.table(downloads)
downloads = downloads[,sum(count), by = "package"]
downloads_new = cran_downloads(packages = c("h2o","ParallelForest", "bigrf"), from = "2017-03-01", to = "2017-03-31" )
downloads_new = data.table(downloads_new)
downloads_new = downloads_new[, sum(count), by = "package"]
downloads = cbind(downloads, downloads_new$V1, round(downloads_new$V1/downloads$V1, 2))
colnames(downloads) = c("**package**", "**--march 2016**", "**--march 2017**", "**--ratio of 2017/2016**")

kable(downloads, format = "markdown", padding = 2)

downloads = cran_downloads(packages = c("rpart", "RRF", "obliqueRF", "rotationForest", 
  "rFerns", "randomUniformForest", "wsrf", "roughrf", "trimTrees", "extraTrees", "party" ), from = "2016-03-01", to = "2016-03-31" )
downloads = data.table(downloads)
downloads = downloads[,sum(count), by = "package"]
ordering = order(downloads$V1, decreasing = T)
downloads = downloads[ordering,]
downloads_new = cran_downloads(packages = c("rpart", "RRF", "obliqueRF", "rotationForest", 
  "rFerns", "randomUniformForest", "wsrf", "roughrf", "trimTrees", "extraTrees", "party" ), from = "2017-03-01", to = "2017-03-31" )
downloads_new = data.table(downloads_new)
downloads_new = downloads_new[, sum(count), by = "package"]
downloads_new = downloads_new[ordering,]
downloads = cbind(downloads, downloads_new$V1, round(downloads_new$V1/downloads$V1, 2))
colnames(downloads) = c("**package**", "**--march 2016**", "**--march 2017**", "**--ratio of 2017/2016**")

kable(downloads, format = "markdown", padding = 2)
```
