---
layout: post
title: Benchmarking algorithms with OpenML and R-package mlr
comments: True
---

The open data platform OpenML offers a lot of dataset to evaluate algorithms. It offers datasets that can be used for 
classification and regression. It is accessible via the [web page](http://www.openml.org/) or via several programming languages 
like Java, Python and R. 

I am performing a benchmark study with datasets from OpenML and classification and regression algorithms from the mlr package, 
which provide standardized interface to many of the algorithms implemented in R and easy evaluation with resampling 
strategies like cross-validation.

The comparison can be found on this github repository: [benchmark-mlr-openml](https://github.com/PhilippPro/benchmark-mlr-openml).

I will soon post a summary about the results. 

Another comparison just between random forest and logistic regression is done by Raphael:
[BenchmarkOpenMl][https://github.com/ShadokRaf/BenchmarkOpenMl].
