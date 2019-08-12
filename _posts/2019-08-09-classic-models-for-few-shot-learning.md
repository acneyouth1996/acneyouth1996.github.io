---
layout: post
comments: true
mathjax: true
priority: 440000
title: “Metric-based few shot learning”
tags: [Machine Learning]
excerpt: Machine Learning
img: manhattan.jpg
date: 2019-08-09 12:00:00
---


## Few shot learning problem setting
Deep learning models have achieved state-of-the-art performance on visual recognition tasks such
as image classification. The strong performance, however, heavily relies on training a network with
abundant labeled instances with diverse visual variations (e.g., thousands of examples for each new
class even with pre-training on large-scale dataset with base classes). The human annotation cost as
well as the scarcity of data in some classes (e.g., rare species) significantly limit the applicability of
current vision systems to learn new visual concepts efficiently. In contrast, the human visual systems
can recognize new classes with extremely few labeled examples. It is thus of great interest to learn
to generalize to new classes with a limited amount of labeled examples for each novel class.

Consider a supervised learning task T , Few Shot Learning deals with a data set $$
D=\left\{D^{\text { train }}, D^{\text { test }}\right\}
$$ consisting of training set $$
D^{\operatorname{train}}=\left\{\left(x^{(i)}, y^{(i)}\right)\right\}_{i=1}^{I}
$$ where $$I$$ is small and test set $$
D^{\text { test }}=\left\{x^{\text { test }}\right\}
$$Usually, people
consider the N-way-K-shot classification task where $$D^{\text { train }}$$ contains $$I = KN$$ examples
from $$N$$ classes each with $$K$$ examples. 

In the next section, I'll talked about some existing models for few-shot learning.
All of these models are distance metric based networks.

## Siamese Network
<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/62896497-2ee66e00-bd1f-11e9-8f7f-ebd5544f4fc7.png" style="border:none;width:50%">
</div>
Siamese network is used for one-shot learning, The basic idea of siamese network is to compare the similarity between the query image feature vector and the labeled image feature vector after the image pair passing through several convolutional layers. After the distance is calculated, a sigmoid function is used to make the final prediction to tell us if the pair is a match or not. The loss function could be cross entropy(in the original paper) or some other fancier loss function such as Contrastive Loss function.


## Matching network
<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/62897573-90a7d780-bd21-11e9-8757-2c1b2a3ba670.png" style="border:none;width:100%">
</div>

Matching network is very similar to siamese network, The major diffrence is that it applies attetion mechanism. The model in its simplest form computes $$\hat{y}$$ as follows
$$
\hat{y}=\sum_{i=1}^{k} a\left(\hat{x}, x_{i}\right) y_{i}
$$
where $$x_{i}, y_{i}$$ are the samples and labels from the support set $$
S=\left\{\left(x_{i}, y_{i}\right)\right\}_{i=1}^{k}
$$, and $$a$$ is an attention mechanism, the attetion used here is the softmax over the cosine distance.

## Prototypical Networks
Prototypical Networks are somehow very similar to matching network, according to its author, Prototypical Networks differ from Matching Networks in the few-shot case with equivalence in the
one-shot scenario.

## Relation-network


