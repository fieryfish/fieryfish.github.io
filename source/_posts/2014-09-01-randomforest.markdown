---
layout: post
title: "RandomForest(随机森林)"
date: 2014-09-01 22:57
comments: true
categories: 
---
RandomForest在我印象里，是一个碉堡了的算法，因为用起来很简单，几乎不需要怎么调试就直接得到了很好地效果。
所以这也是为什么在Kaggle的Titanic分类竞赛专门拿RandomForest[举例](http://www.kaggle.com/c/titanic-gettingStarted/details/getting-started-with-random-forests).
在学习RandomForest之前，请保证大家已经基本掌握了Decision Tree。[这里](http://www.quora.com/How-do-random-forests-work-in-laymans-terms)有个很好的对于RandomForest的解释。

它的主要思想是将Decision Tree 聚合(ensemble)。根据[wiki](http://en.wikipedia.org/wiki/Random_forest),它的实现主要
通过了bagging和Random subspace method两个方法。为什么要用这两个方法呢? Randomness!
前者是针对训练数据的随机选择，比如1000个实例中随机选择800个去生成树。
它的强悍之处在我看来是因为wiki里的这句话 "Increasing the number of trees tends to decrease the variance of the model,
without increasing the bias." Decision Tree的问题之一就是容易overfitting,显然bagging很好的解决了这个问题（更多关于overfitting请参考[维数灾难到学习曲线](http://bboxers.com/blog/2014/06/29/curse-learning-curve/))。
后者是针对feature去进行选择，比如从100个
features里面去选择50个,这使得每一个子树都不完全相同。之后再根据正常的Decision Trees算法去生成树，这里有个参数（free parameter）需要用户选择，
就是生成多少个trees，一般来讲，当然是越多越好了，最后根据各个trees的结果去投票(vote)，算出最终的结果。
但是tree多了以后运算会超级慢，这是个不得不要考虑进去的问题，准确+慢 vs 不准确+快？
这时候，Out-of-bag error就是判定生成多大树的标准之一了，[quora](http://www.quora.com/What-is-the-out-of-bag-error-in-Random-Forests)有个很好的解释，
大概就是说，在我们bagging的时候，从1000个实例选了800个，那么剩下的那200就组成了out-of-bag examples，
我们可以把这200个实例组成测试的trees，这样通过测试trees就可以大概知道RandomForest的精确度。

