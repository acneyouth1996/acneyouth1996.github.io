---
layout: post
comments: true
mathjax: true
priority: 440000
title: "Neural ODEs in Detail"
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
4. Euler methods to solver ode: Euler methods is the simplest ode solver,
$$
\mathbf{z}(t+h)=\mathbf{z}(t)+h f(\mathbf{z}, t)
$$




## Similarity between Residual Block and Euler methods

As you can see from the above equation, there is a some similarity between euler methods and residual block design.

Example of ResNet:

```python
def resnet(z, theta):
    for t in [1:T]:
        z = z + f(z,t,theta)
    return z
```

The key idea of ODE-Net is to make hidden states continuously defined with depth, in other words, use a 
ODE solver to replace the whole for loop in resnet.

```python
def odenet(z, t, theta):
    # zero and one is the start time and end time
    return ODESolve(f, z , 0, 1, theta)
    
```


## How to train ODE net?


The objective we want to optimize can be written in this:

$$
L(\theta)=L\left(\int_{t_{0}}^{t_{1}} f(\mathbf{z}(t), t, \theta) d t\right)
$$

and we want to know 

$$
\frac{\partial L}{\partial \theta}
$$

Simplily do backpropagration in ODE-net is a bad idea for two reasons:

1. High memory cost.
2. Extra numerical error.

There is another easier and more efficient method introduced in this paper, and the basic idea is to do back propagration via another ode solver.

## Adjoint state and Reverse-mode derivative(backpropagration) of an ODE initial value problem

To optimize $$L$$ one needs to compute the gradients wrt. its parameters: $$z(t_0), t_0, t_1, \theta$$.

the dynamic of adjoint could be depicted by another ODE which is :

$$
\frac{d \mathbf{a}(t)}{d t}=-\mathbf{a}(t) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}(t)}
$$

**Proof**

With a continuous hidden state, we can write the transformation after an $$\varepsilon$$ change in time as 

$$
\mathbf{z}(t+\varepsilon)=\int_{t}^{t+\varepsilon} f(\mathbf{z}(t), t, \theta) d t+\mathbf{z}(t)=T_{\varepsilon}(\mathbf{z}(t), t)
$$

By chain rule we have 

$$
\frac{d L}{\partial \mathbf{z}(t)}=\frac{d L}{d \mathbf{z}(t+\varepsilon)} \frac{d \mathbf{z}(t+\varepsilon)}{d \mathbf{z}(t)}
$$

or 

$$
\mathbf{a}(t)=\mathbf{a}(t+\varepsilon) \frac{\partial T_{\varepsilon}(\mathbf{z}(t), t)}{\partial \mathbf{z}(t)}
$$

follow the definition of 


 

















