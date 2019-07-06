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


