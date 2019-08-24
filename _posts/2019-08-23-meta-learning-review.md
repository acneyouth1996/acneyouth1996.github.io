---
layout: post
comments: true
mathjax: true
priority: 440000
title: "Meta-Learning: Learning to Learn Fast"
tags: [Machine Learning]
excerpt: Machine Learning
img: ri.jpg
date: 2019-08-26 00:00:00
---

## Introduction
Meta-learning, also known as "learning to learn", intends to design models that can learn new skills or adapt to new environments rapidly with a few training examples. There are three common approaches: 1) learn an efficient distance metric (metric-based); 2) use (recurrent) network with external or internal memory (model-based); 3) optimize the model parameters explicitly for fast learning (optimization-based).

A good machine learning model often requires training with a large number of samples. Humans, in contrast, learn new concepts and skills much faster and more efficiently. Kids who have seen cats and birds only a few times can quickly tell them apart. People who know how to ride a bike are likely to discover the way to ride a motorcycle fast with little or even no demonstration. Is it possible to design a machine learning model with similar properties --- learning new concepts and skills fast with a few training examples? That's essentially what **meta-learning** aims to solve.

We expect a good meta-learning model capable of well adapting or generalizing to new tasks and new environments that have never been encountered during training time. The adaptation process, essentially a mini learning session, happens during test but with a limited exposure to the new task configurations. Eventually, the adapted model can complete new tasks. This is why meta-learning is also known as [learning to learn](https://www.cs.cmu.edu/~rsalakhu/papers/LakeEtAl2015Science.pdf). 

The tasks can be any well-defined family of machine learning problems: supervised learning, reinforcement learning, etc. For example, here are a couple concrete meta-learning tasks:

- A classifier trained on non-cat images can tell whether a given image contains a cat after seeing a handful of cat pictures.
- A game bot is able to quickly master a new game.
- A mini robot completes the desired task on an uphill surface during test even through it was only trained in a flat surface environment.

## Define the Meta-Learning Problem

In this post, we focus on the case when each desired task is a supervised learning problem like image classification. There is a lot of interesting literature on meta-learning with reinforcement learning problems (aka "Meta Reinforcement Learning"), but we would not cover them here.


### A Simple View

A good meta-learning model should be trained over a variety of learning tasks and optimized for the best performance on a distribution of tasks, including potentially unseen tasks. Each task is associated with a dataset $$\mathcal{D}$$, containing both feature vectors and true labels. The optimal model parameters are:

$$
\theta^* = \arg\min_\theta \mathbb{E}_{\mathcal{D}\sim p(\mathcal{D})} [\mathcal{L}_\theta(\mathcal{D})]
$$

It looks very similar to a normal learning task, but *one dataset* is considered as *one data sample*. 

*Few-shot classification* is an instantiation of meta-learning in the field of supervised learning. The dataset $$\mathcal{D}$$ is often split into two parts, a support set $$S$$ for learning and a prediction set $$B$$ for training or testing, $$\mathcal{D}=\langle S, B\rangle$$. Often we consider a *K-shot N-class classification* task: the support set contains K labelled examples for each of N classes.


### Learner and Meta-Learner

Another popular view of meta-learning decomposes the model update into two stages:
- A classifier $$f_\theta$$ is the "learner" model, trained for operating a given task;
- In the meantime, a optimizer $$g_\phi$$ learns how to update the learner model's parameters via the support set $$S$$, $$\theta' = g_\phi(\theta, S)$$.

Then in final optimization step, we need to update both $$\theta$$ and $$\phi$$ to maximize:

$$
\mathbb{E}_{L\subset\mathcal{L}}[ \mathbb{E}_{S^L \subset\mathcal{D}, B^L \subset\mathcal{D}} [\sum_{(\mathbf{x}, y)\in B^L} P_{g_\phi(\theta, S^L)}(y \vert \mathbf{x})]]
$$


### Common Approaches

There are three common approaches to meta-learning: metric-based, model-based, and optimization-based. Oriol Vinyals has a nice summary in his [talk](http://metalearning-symposium.ml/files/vinyals.pdf) at meta-learning symposium @ NIPS 2018:

{: class="info"}
| ------------- | ------------- | ------------- | ------------- |
|  | Model-based | Metric-based | Optimization-based |
| ------------- | ------------- | ------------- | ------------- |
| **Key idea** | RNN; memory | Metric learning | Gradient descent |
| **How $$P_\theta(y \vert \mathbf{x})$$ is modeled?** | $$f_\theta(\mathbf{x}, S)$$ | $$\sum_{(\mathbf{x}_i, y_i) \in S} k_\theta(\mathbf{x}, \mathbf{x}_i)y_i$$ (*) | $$P_{g_\phi(\theta, S^L)}(y \vert \mathbf{x})$$ |

(*) $$k_\theta$$ is a kernel function measuring the similarity between $$\mathbf{x}_i$$ and $$\mathbf{x}$$.

Next we are gonna review classic models in each approach.

## Metric-Based

The core idea in metric-based meta-learning is similar to nearest neighbors algorithms (i.e., [k-NN](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm) classificer and [k-means](https://en.wikipedia.org/wiki/K-means_clustering) clustering) and [kernel density estimation](https://en.wikipedia.org/wiki/Kernel_density_estimation). The predicted probability over a set of known labels $$y$$ is a weighted sum of labels of support set samples. The weight is generated by a kernel function $$k_\theta$$, measuring the similarity between two data samples.

$$
P_\theta(y \vert \mathbf{x}, S) = \sum_{(\mathbf{x}_i, y_i) \in S} k_\theta(\mathbf{x}, \mathbf{x}_i)y_i 
$$ 


To learn a good kernel is crucial to the success of a metric-based meta-learning model. [Metric learning](https://en.wikipedia.org/wiki/Similarity_learning#Metric_learning) is well aligned with this intention, as it aims to learn a metric or distance function over objects. The notion of a good metric is problem-dependent. It should represent the relationship between inputs in the task space and facilitate problem solving.

All the models introduced below learn embedding vectors of input data explicitly and use them to design proper kernel functions.


### Convolutional Siamese Neural Network

The [Siamese Neural Network](https://papers.nips.cc/paper/769-signature-verification-using-a-siamese-time-delay-neural-network.pdf) is composed of two twin networks and their outputs are jointly trained on top with a function to learn the relationship between pairs of input data samples. The twin networks are identical, sharing the same weights and network parameters. In other words, both refer to the same embedding network that learns an efficient embedding to reveal relationship between pairs of data points.

[Koch, Zemel & Salakhutdinov (2015)](http://www.cs.toronto.edu/~rsalakhu/papers/oneshot1.pdf) proposed a method to use the siamese neural network to do one-shot image classification. First, the siamese network is trained for a verification task for telling whether two input images are in the same class. It outputs the probability of two images belonging to the same class. Then, during test time, the siamese network processes all the image pairs between a test image and every image in the support set. The final prediction is the class of the support image with the highest probability.


1. First, convolutional siamese network learns to encode two images into feature vectors via a embedding function $$f_\theta$$ which contains a couple of convolutional layers. 
2. The L1-distance between two embeddings is $$\vert f_\theta(\mathbf{x}_i) - f_\theta(\mathbf{x}_j) \vert$$.
3. The distance is converted to a probability $$p$$ by a linear feedforward layer and sigmoid. It is the probability of whether two images are drawn from the same class.
4. Intuitively the loss is cross entropy because the label is binary.

$$
\begin{aligned}
p(\mathbf{x}_i, \mathbf{x}_j) &= \sigma(\mathbf{W}\vert f_\theta(\mathbf{x}_i) - f_\theta(\mathbf{x}_j) \vert) \\
\mathcal{L}(B) &= \sum_{(\mathbf{x}_i, \mathbf{x}_j, y_i, y_j)\in B} \mathbf{1}_{y_i=y_j}\log p(\mathbf{x}_i, \mathbf{x}_j) + (1-\mathbf{1}_{y_i=y_j})\log (1-p(\mathbf{x}_i, \mathbf{x}_j))
\end{aligned}
$$


Images in the training batch $$B$$ can be augmented with distortion. Of course, you can replace the L1 distance with other distance metric, L2, cosine, etc. Just make sure they are differential and then everything else works the same.

Given a support set $$S$$ and a test image $$\mathbf{x}$$, the final predicted class is:

$$
\hat{c}_S(\mathbf{x}) = c(\arg\max_{\mathbf{x}_i \in S} P(\mathbf{x}, \mathbf{x}_i))
$$

where $$c(\mathbf{x})$$ is the class label of an image $$\mathbf{x}$$ and $$\hat{c}(.)$$ is the predicted label.

The assumption is that the learned embedding can be generalized to be useful for measuring the distance between images of unknown categories. This is the same assumption behind transfer learning via the adoption of a pre-trained model; for example, the convolutional features learned in the model pre-trained with ImageNet are expected to help other image tasks. However, the benefit of a pre-trained model decreases when the new task diverges from the original task that the model was trained on.


### Matching Networks

The task of **Matching Networks** ([Vinyals et al., 2016](http://papers.nips.cc/paper/6385-matching-networks-for-one-shot-learning.pdf)) is to learn a classifier $$c_S$$ for any given (small) support set $$S=\{x_i, y_i\}_{i=1}^k$$ (*k-shot* classification). This classifier defines a probability distribution over output labels $$y$$ given a test example $$\mathbf{x}$$. Similar to other metric-based models, the classifier output is defined as a sum of labels of support samples weighted by attention kernel $$a(\mathbf{x}, \mathbf{x}_i)$$ - which should be proportional to the similarity between $$\mathbf{x}$$ and $$\mathbf{x}_i$$.




$$
c_S(\mathbf{x}) = P(y \vert \mathbf{x}, S) = \sum_{i=1}^k a(\mathbf{x}, \mathbf{x}_i) y_i
\text{, where }S=\{(\mathbf{x}_i, y_i)\}_{i=1}^k
$$

The attention kernel depends on two embedding functions, $$f$$ and $$g$$, for encoding the test sample and the support set samples respectively. The attention weight between two data points is the cosine similarity, $$\text{cosine}(.)$$, between their embedding vectors, normalized by softmax:

$$
a(\mathbf{x}, \mathbf{x}_i) = \frac{\exp(\text{cosine}(f(\mathbf{x}), g(\mathbf{x}_i))}{\sum_{j=1}^k\exp(\text{cosine}(f(\mathbf{x}), g(\mathbf{x}_j))}
$$










