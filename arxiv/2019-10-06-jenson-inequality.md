---
layout: post
comments: true
mathjax: true
priority: 440000
title: “Jensen Inequality”
tags: [math & stats]
excerpt: math
img: ri.jpg
date: 2019-10-05 12:00:00
---

If $$p_{1}, \dots, p_{n}$$ are positive numbers which sum to 1 and $$f$$ is a real continuous function that is convex, then


$$
f\left(\sum_{i=1}^{n} p_{i} x_{i}\right) \leq \sum_{i=1}^{n} p_{i} f\left(x_{i}\right)
$$

If f is concave, then the inequality reverses, giving

$$
f\left(\sum_{i=1}^{n} p_{i} x_{i}\right) \geq \sum_{i=1}^{n} p_{i} f\left(x_{i}\right)
$$

One special case:

when $$p_{i}=\frac{1}{n}$$ with the concave function $$\ln(x)$$ gives

$$
\ln \left(\frac{1}{n} \sum_{i=1}^{n} x_{i}\right) \geq \frac{1}{n} \sum_{i=1}^{n} \ln x_{i}
$$


which can be exponentiated to give the arithmetic mean-geometric mean inequality


$$
\frac{x_{1}+x_{2}+\ldots+x_{n}}{n} \geq \sqrt[n]{x_{1} x_{2} \cdots x_{n}}
$$

Here, equality holds iff $$
x_{1}=x_{2}=\ldots=x_{n}
$$