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

$$z(t_0)$$ is the hidden state at time $$t_0$$

The first term $$
\frac{d L}{d \mathbf{z}(t)}
$$ is called **Adjoint** in this paper. The adjoint state is actually the gradient with respect to the hidden state at a specified time t and the dynamic of adjoint can actually be modeled by another ODE:

 
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

follow the definition of derivative:


 $$
\begin{aligned} \frac{d \mathbf{a}(t)}{d t} &=\lim _{\varepsilon \rightarrow 0^{+}} \frac{\mathbf{a}(t+\varepsilon)-\mathbf{a}(t)}{\varepsilon} \\ &=\lim _{\varepsilon \rightarrow 0^{+}} \frac{\mathbf{a}(t+\varepsilon)-\mathbf{a}(t+\varepsilon) \frac{\partial}{\partial \mathbf{z}(t)} T_{\varepsilon}(\mathbf{z}(t))}{\varepsilon} \\ &=\lim _{\varepsilon \rightarrow 0^{+}} \frac{\mathbf{a}(t+\varepsilon)-\mathbf{a}(t+\varepsilon) \frac{\partial}{\partial \mathbf{z}(t)}\left(\mathbf{z}(t)+\varepsilon f(\mathbf{z}(t), t, \theta)+\mathcal{O}\left(\varepsilon^{2}\right)\right)}{\varepsilon} \\ &=\lim _{\varepsilon \rightarrow 0^{+}} \frac{\mathbf{a}(t+\varepsilon)-\mathbf{a}(t+\varepsilon)\left(I+\varepsilon \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial z(t)}+\mathcal{O}\left(\varepsilon^{2}\right)\right)}{\varepsilon} \\ &=\lim _{\varepsilon \rightarrow 0^{+}} \frac{-\varepsilon \mathbf{a}(t+\varepsilon) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}(t)}+\mathcal{O}(\varepsilon)}{\varepsilon \mathbf{z}(t)}+\mathcal{O}(\varepsilon) \\ &=-\mathbf{a}(t) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}(t)} \end{aligned}
$$

To get the value of $$a(t)$$, we simply need to call a ODE solver and specify the constraint on the last time
point (any value defined by us) .The adjoint at $$t_0$$ is 

$$
\mathbf{a}\left(t_{0}\right)=\mathbf{a}\left(t_{N}\right)+\int_{t_{N}}^{t_{0}} \frac{d \mathbf{a}(t)}{d t} d t=\frac{d L}{d \mathbf{z}\left(t_{N}\right)}-\int_{t_{N}}^{t_{0}} \mathbf{a}(t)^{T} \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}(t)}
$$

Similarly, the derivative with respect to $$\theta$$ can be also calculated. First we set $$
\frac{d L}{d \theta}=\mathbf{a}_{\theta}\left(t_{N}\right) = 0
$$


$$
\begin{aligned}
\frac{d L}{d \theta}&= \frac{d L}{d z(t)} \frac{d z(t)}{d \theta} \\ &=
\int_{t_{0}}^{t_{N}} \mathbf{a}(t) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}(t)} \frac{\partial \mathbf{z}(t)}{\partial \theta}\\ &= \int_{t_{0}}^{t_{N}} \mathbf{a}(t) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \theta}
\end{aligned}
$$



Finally, we also get gradients with respect to $$t_0$$ and $$t_N$$ , the start and end of the integration interval.

$$
\frac{d L}{d t_{N}}=\mathbf{a}\left(t_{N}\right) f\left(\mathbf{z}\left(t_{N}\right), t_{N}, \theta\right) \quad \frac{d L}{d t_{0}}=\mathbf{a}_{t}\left(t_{0}\right)=\mathbf{a}_{t}\left(t_{N}\right)-\int_{t_{N}}^{t_{0}} \mathbf{a}(t) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial t} d t
$$


To calculate all these derivatives in one single call of ODE solver, the author wrap up everything into one variable called augmented state. The details are in original paper Appendix B.2.

