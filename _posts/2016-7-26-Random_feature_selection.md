---
layout: post
title: Random feature selection
comments: True
---

As mentioned in the previous post I will write a bit about the random feature selection in random forest. In the training step 
at each split in a random forest k features are selected at random from all features. For these features the ideal split 
according to a split criteria is chosen and the feature which performs best under all features is chosen as feature. 
The number k should not be set too high, so that not always the same features are chosen, but also not too small, so that at 
least some relevant features come into the comparison. It also depends highly on the dataset. If in the dataset there are only 
few features with relevance, the number k should be set sufficiently high, and vice versa.

The introduction of randomness in the feature selection process seems to be advantageous in many cases. The default in 
many packages like **randomForest** is to choose $latex k = \sqrt(p)$ features at random at each split. 
