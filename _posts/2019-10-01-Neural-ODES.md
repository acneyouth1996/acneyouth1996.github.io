---
layout: post
comments: true
mathjax: true
priority: 440000
title: "Neural ODEs in very detail"
tags: [Machine Learning]
excerpt: Machine Learning
img: ri.jpg
date: 2019-10-01 00:00:00
---

## ODE recap
1. Vector $$\mathbf{z}$$ changes in time
2. Derivative wrt. time $$ \frac{d \mathbf{z}}{d t}=f(\mathbf{z}(t), t)$$
3. Initial-value problem: what we usually do with ode is given $$\mathbf{z}(t_{0})$$, try to find
$$
\mathbf{z}\left(t_{1}\right)=\mathbf{z}\left(t_{0}\right)+\int_{t_{0}}^{t_{1}} f(\mathbf{z}(t), t, \theta) d t
$$ 
4. Euler methods to solver ode: 
Euler methods is the simplest ode solver:
$$
\mathbf{z}(t+h)=\mathbf{z}(t)+h f(\mathbf{z}, t)
$$


## Similarity between Residual Block and Euler methods


