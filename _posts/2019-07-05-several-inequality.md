---
layout: post
comments: true
mathjax: true
priority: 440000
title: “Several Inequality”
tags: [math]
excerpt: math
img: math.png
date: 2019-07-05 12:00:00
---

## Markov’s Inequality
We start with an inequality which is called Markov’s inequality. Let $$Z$$ be a nonnegative random variable. The expectation of $$Z$$ can be written as follows:

$$
\mathbb{E}[Z]=\int_{x=0}^{\infty} \mathbb{P}[Z \geq x] d x
$$

Since $$\mathbb{P}[Z \geq x$$ is monotonically nonincreasing we obtain

$$
\forall a \geq 0, \quad \mathbb{E}[Z] \geq \int_{x=0}^{a} \mathbb{P}[Z \geq x] d x \geq \int_{x=0}^{a} \mathbb{P}[Z \geq a] d x=a \mathbb{P}[Z \geq a]
$$

Rearranging the inequality yields Markov’s inequality:

$$
\forall a \geq 0, \quad \mathbb{P}[Z \geq a] \leq \frac{\mathbb{E}[Z]}{a}
$$

1. LEMMMA1: Let $$$$ be a random variable that takes values in [0, 1]. Assume that $$
\mathbb{E}[Z]=\mu
$$ Then, for any $$a \in(0,1) $$,

$$
\mathbb{P}[Z>1-a] \geq \frac{\mu-(1-a)}{a}
$$

This also implies that for every $$a \in(0,1) $$,
$$
\mathbb{P}[Z>a] \geq \frac{\mu-a}{1-a} \geq \mu-a
$$

## Chebyshev’s Inequality

Applying Markov’s inequality on the random variable $$(Z-\mathbb{E}[Z])^{2}$$ we obtain Chebyshev’s inequality:

$$
\forall a>0, \quad \mathbb{P}[|Z-\mathbb{E}[Z]| \geq a]=\mathbb{P}\left[(Z-\mathbb{E}[Z])^{2} \geq a^{2}\right] \leq \frac{\operatorname{Var}[Z]}{a^{2}}
$$

where $$\operatorname{Var}[Z]=\mathbb{E}\left[(Z-\mathbb{E}[Z])^{2}\right]$$ is the variance of $$Z$$.

Consider the random variable $$\frac{1}{m} \sum_{i=1}^{m} Z_{i}$$ . Since $$Z_{1}, \ldots, Z_{m}$$ are i.i.d. it is easy to verify that

$$
\operatorname{Var}\left[\frac{1}{m} \sum_{i=1}^{m} Z_{i}\right]=\frac{\operatorname{Var}\left[Z_{1}\right]}{m}
$$

Applying Chebyshev’s inequality, we obtain the following:

1. LEMMA 2: Let $$Z_{1}, \ldots, Z_{m}$$ be a sequence of i.i.d. random variables and assume that $$\mathbb{E}\left[Z{1}\right]=\mu
$$ and $$
\operatorname{Var}\left[Z_{1}\right] \leq 1
$$. Then, for any $$
\delta \in(0,1)
$$, with probability of at least $$
1-\delta
$$ we have

$$
\left|\frac{1}{m} \sum_{i=1}^{m} Z_{i}-\mu\right| \leq \sqrt{\frac{1}{\delta m}}
$$

The deviation between the empirical average and the mean given previously decreases polynomially with m. It is possible to obtain a significantly faster decrease. In the sections that follow we derive bounds that decrease exponentially fast.

## Hoeffding’s Inequality

Let $$Z_{1}, \ldots, Z_{m}$$ be a sequence of i.i.d. random variables and let $$ \overline{Z}=\frac{1}{m} \sum_{i=1}^{m} Z_{i}
$$ Assume that $$ 
\mathbb{E}[\overline{Z}]=\mu
$$ and $$
\mathbb{P}[a \leq Z_{i} \leq b ]=1
$$  for every $$i$$. Then, for any $$
\epsilon>0
$$, 
$$
\mathbb{P}\left[\left|\frac{1}{m} \sum_{i=1}^{m} Z_{i}-\mu\right|>\epsilon\right] \leq 2 \exp \left(-2 m \epsilon^{2} /(b-a)^{2}\right)
$$

Proof: Denote $$
X_{i}=Z_{i}-\mathbb{E}\left[Z_{i}\right]
$$ and $$
\overline{X}=\frac{1}{m} \sum_{i} X_{i}
$$ Using the monotonicity of the exponent function and Markov’s inequality, we have that for every 
$$\lambda>0$$, $$\epsilon>0$$, 
$$
\mathbb{P}[\overline{X} \geq \epsilon]=\mathbb{P}\left[e^{\lambda \overline{X}} \geq e^{\lambda \epsilon}\right] \leq e^{-\lambda \epsilon} \mathbb{E}\left[e^{\lambda \overline{X}}\right]
$$

Using the independence assumption we also have 
$$
\mathbb{E}\left[e^{\lambda \overline{X}}\right]=\mathbb{E}\left[\prod_{i} e^{\lambda X_{i} / m}\right]=\prod_{i} \mathbb{E}\left[e^{\lambda X_{i} / m}\right]
$$



