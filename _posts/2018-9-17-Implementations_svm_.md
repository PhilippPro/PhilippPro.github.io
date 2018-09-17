---
title: "Implementations and defaults of the Support Vector Machine in R"
layout: post
output:
  html_document: default
  pdf_document: default
comments: yes
---

In this article I will present a benchmark analysis of different implementations of support vector machines in R. 

<!--excerpt-->

## Different packages

The different packages that I will compare are the most popular package **e1071**, the also well known package **kernlab** and the less well known package **liquidSVM**. 
I will use 38 binary classification datasets of the [OpenML100](https://arxiv.org/abs/1708.03731), that I already used for other benchmark analysis. 

I compare these three packages in their default version. All packages use the radial kernel as default. In **e1071** $\gamma$ is set to $1/p$ with  $p$ being the number of observations and the cost parameter is set to 1. In **kernlab** the cost parameter is also set to 1 and $\gamma$ is set by an automatic procedure/heuristic that was developed by [Caputo et. al](https://www.researchgate.net/publication/239415588_Appearance-based_Object_Recognition_using_SVMs_Which_Kernel_Should_I_Use).





