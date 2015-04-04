---
layout: post
title: "概率模型probabilistic models (1)"
date: 2014-06-16 15:34
comments: true
categories: 
---
之前说到我在公司是做Ruby on rails程序员，后来转到数据这块（小公司+好老板的好处就是可以无损的转去做自己喜欢的项目），
面临的第一个项目就是做一个分类器，而我第一个采用的第一个模型就是概率模型(probabilistic models)，
具体分类器是[朴素贝叶斯分类器](http://zh.wikipedia.org/zh/%E6%9C%B4%E7%B4%A0%E8%B4%9D%E5%8F%B6%E6%96%AF%E5%88%86%E7%B1%BB%E5%99%A8)。
效果在当时的我们看来，可以总结为三个字：碉堡了。我当时看到结果真的蛮震惊的，从前上课时候一扫而过的贝叶斯公式竟然有如此威力！

首先，如果对于朴素贝叶斯或者贝叶斯公式还不打了解的同学，我的建议是：拿出一张纸和一支笔，去[阮一峰的blog](http://www.ruanyifeng.com/blog/2013/12/naive_bayes_classifier.html)
搜索、学习贝叶斯相关内容。（阮一峰的blog很有文采，强烈推荐之）。
在我看到‘朴素贝叶斯(Naive Bayes)’这几个字以后，对我来讲比较敏感的字眼其实是‘朴素(Naive)’两个字，
贝叶斯就贝叶斯，怎么还naive呢？
其实，他表示的是一个简化的假设（assumption），即假设数据的特征(feature)/属性/字段是条件独立的(conditional independent)，why？
举个例子（引用我的导师[Novi](http://www.sussex.ac.uk/profiles/335583/publications)）：假设我们要训练样例是20个features组成的，每个feature都是取值二元的（即可以取0或者1，no或者yes），
样例的类别（class label）也是二元的，

$$
p(X_{1},X_{2}..X_{20}|Y)
$$

那么，概率的估计（probability estimates）有多少种可能呢？也就是假设空间(hypothesis space)的大小是多少？
我的答案：
$2^{21} -1=2097151个$
（每个Feature有两种，class label有两种，减1是因为概率之和必须为1，减去一个自由度）

所以这就导致了我们至少要学习2097151个样例才可以保证贝叶斯公式可以用，是不是很恐怖。
这也就是为什么我们需要一个条件独立假设，即 

$$
P(X_{1},X_{2}..X_{20}|Y) := P(X_{1}|Y)\cdot P(X_{2}|Y)...P(X_{20}|Y)
$$

这时候，概率的估计（probability estimates）有多少种可能呢？
我的答案是41个，
（我的解释：对于每一项P(X|Y)来说，给定了Y值，X的自由度是1，因为其概率和是1，而Y有两种可能取值，所以一共2*20=40,
再加上P(Y)还有一个自由度，一共就是41）
显然，naive Bayes小了很多，对于训练的要求减小了。
那再引出一个问题，什么是‘不朴素(non-naive)’的分类器呢？

回归到我当时的任务，简单场景就是：每个商铺/网店都有一些描述信息，我们想根据字段的信息（比如‘地址address’）把他们分类到相关城市city去。
做这个任务的模型很简单，首先将地址分词，得到word1,word2,word3...,得到相应公式(以分成两个词为例)：

$$
P(Y_{city}|X_{word1},X_{word2})=\frac{P(X_{word1}|Y_{city})\cdot P(X_{word2}|Y_{city})\cdot (p_{Y_{city}})}{P(X_{word1})\cdot P(X_{word2})}
$$

由于分类时候，分子都是是一样的，可以不考虑。再假设
$P(Y_{city})$
是均匀分布(uniform distribution)，又可以
忽略这个值（但不总是满足uniform distribution, 先验知识有时候是很重要的），那么最终的分类过程相当于 

$$
P^{\ast}(Y_{city}|X_{word1},X_{word2})=\operatorname{argmax}_Y (P(X_{word1}|Y_{city})\cdot P(X_{word2}|Y_{city}))
$$

在实际应用时候，naive Bayes有个特别的优势：有些地址是不太好判别，而这一类的地址，在计算后验概率的时候，
概率较大的几组概率都很相近，导致很难‘确信’地把这些样例分类到相关城市。比如: (长春和北京都有朝阳区哦~)

$$
P(Y_{北京}|X_{朝阳区})=0.3 , ~~
P(Y_{长春}|X_{朝阳区})=0.33
$$

那么通过naive Bayes可以加一些简单的判断去提取这些很容易出错的实例，是不是很有用呢。^-^
另外,先验知识(prior knowledge)也可以被当做因素考虑进来，例如：根据上面的概率
$$P(Y_{长春}|X_{朝阳区}) > P(Y_{北京}|X_{朝阳区})$$
因此，这个实例应该被分到**长春**。

但是考虑先验知识
$$P(Y_{北京})=0.5, ~~ P(Y_{长春})=0.3$$

那么
$$P(Y_{长春}|X_{朝阳区})\cdot P(Y_{长春}) < P(Y_{北京}|X_{朝阳区})\cdot P(Y_{北京})$$
因此，这个实例应该被分到**北京**。


