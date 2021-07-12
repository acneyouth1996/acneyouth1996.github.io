---
layout: post
comments: true
mathjax: true
priority: 440000
title: “Concave and Convex function”
tags: [math & stats]
excerpt: math
img: es.jpeg
date: 2019-10-05 12:00:00
---

## Definition


A real-valued function $$f$$ on an interval (or, more generally, a [convex set](https://en.wikipedia.org/wiki/Convex_set) in vector space) is said to be concave if, for any $$x$$ and $$y$$ in the interval and for any $$ \alpha \in [0,1]$$.

$$
f((1-\alpha) x+\alpha y) \geq(1-\alpha) f(x)+\alpha f(y)
$$


A function is called strictly concave if


$$
f((1-\alpha) x+\alpha y)>(1-\alpha) f(x)+\alpha f(y)
$$

for any $$\alpha \in [0,1]$$ and $$x \neq y$$


For a function $$f: \mathbb{R} \rightarrow \mathbb{R}$$, this second definition merely states that for every $$z$$ strictly between $$x$$ and $$y$$, the point $$ (z,f(z))$$ on the graph of $$f$$ is above the straight line joining the points $$(x,f(x))$$ and $$ (y,f(y))$$.


Convex is the opposite of concave case.