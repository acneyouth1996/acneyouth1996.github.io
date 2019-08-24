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

### Introduction
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










