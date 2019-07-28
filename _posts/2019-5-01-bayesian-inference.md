---
layout: post
comments: true
mathjax: true
priority: 440000
title: “Bayesian Inference”
tags: [Machine Learning]
excerpt: Machine Learning
img: nyc.jpeg
date: 2019-05-01 12:00:00
---
## Bayesian Stats and Frequentist
In frequentist's perspective, the true parameters value $$\theta$$ is fixed but unknown, while the point estimate $$\hat{\theta}$$ is a random variable being a function of the dataset. The Bayesian uses probability to reflect degrees of certainty in states of knowledge. The dataset is directly observed but not random. The true parameter is unknown, hence it is represented as a random variable.

1. Uncertainty in a given point estimare of $$\theta$$
The frequentist's approach to address the uncertainty in a given point estimate of $$\theta$$ is by evaluating its variance. The variance of the estimator is an assessment of the observed data. The Bayesian answer to the question is to simply integrate over it.

2. The contribution of the Bayesian prior distribution
The prior has an influence by shifting probability mass density towards regions of the parameter space that are preferred by a priori. In practice, the prior often expressed a preference for models that are simpler or more smooth.

## Bayesian Linear Regression
Expressed as a Gaussian conditional distribution on $$y^{train}$$, we have
$$
p\left(\boldsymbol{y}^{(\text { train })} | \boldsymbol{X}^{(\text { train })}, \boldsymbol{w}\right)=\mathcal{N}\left(\boldsymbol{y}^{(\text { train })} ; \boldsymbol{X}^{(\operatorname{train})} \boldsymbol{w}, \boldsymbol{I}\right)
$$

$$
\propto \exp \left(-\frac{1}{2}\left(\boldsymbol{y}^{(\operatorname{train})}-\boldsymbol{X}^{(\operatorname{train})} \boldsymbol{w}\right)^{\top}\left(\boldsymbol{y}^{(\operatorname{train})}-\boldsymbol{X}^{(\operatorname{train})} \boldsymbol{w}\right)\right)
$$

To determine the posterior distribution over the model parameter vector $w$, we ﬁrst need to specify a prior distribution. For real-valued parameters it is common to use a Gaussian as a prior distribution

$$
p(\boldsymbol{w})=\mathcal{N}\left(\boldsymbol{w} ; \boldsymbol{\mu}_{0}, \boldsymbol{\Lambda}_{0}\right) \propto \exp \left(-\frac{1}{2}\left(\boldsymbol{w}-\boldsymbol{\mu}_{0}\right)^{\top} \boldsymbol{\Lambda}_{0}^{-1}\left(\boldsymbol{w}-\boldsymbol{\mu}_{0}\right)\right)
$$


With the prior thus speciﬁed, we can now proceed in determining the posterior distribution over the model parameters：
$$p(\boldsymbol{w} | \boldsymbol{X}, \boldsymbol{y}) \propto p(\boldsymbol{y} | \boldsymbol{X}, \boldsymbol{w}) p(\boldsymbol{w})$$

$$
\propto \exp \left(-\frac{1}{2}(\boldsymbol{y}-\boldsymbol{X} \boldsymbol{w})^{\top}(\boldsymbol{y}-\boldsymbol{X} \boldsymbol{w})\right) \exp \left(-\frac{1}{2}\left(\boldsymbol{w}-\boldsymbol{\mu}_{0}\right)^{\top} \boldsymbol{\Lambda}_{0}^{-1}\left(\boldsymbol{w}-\boldsymbol{\mu}_{0}\right)\right)
$$

$$
\propto \exp \left(-\frac{1}{2}\left(-2 \boldsymbol{y}^{\top} \boldsymbol{X} \boldsymbol{w}+\boldsymbol{w}^{\top} \boldsymbol{X}^{\top} \boldsymbol{X} \boldsymbol{w}+\boldsymbol{w}^{\top} \mathbf{\Lambda}_{0}^{-1} \boldsymbol{w}-2 \boldsymbol{\mu}_{0}^{\top} \mathbf{\Lambda}_{0}^{-1} \boldsymbol{w}\right)\right)
$$

We now deﬁne $$
\boldsymbol{\Lambda}_{m}=\left(\boldsymbol{X}^{\top} \boldsymbol{X}+\mathbf{\Lambda}_{0}^{-1}\right)^{-1}
$$ and $$
\boldsymbol{\mu}_{m}=\boldsymbol{\Lambda}_{m}\left(\boldsymbol{X}^{\top} \boldsymbol{y}+\mathbf{\Lambda}_{0}^{-1} \boldsymbol{\mu}_{0}\right)
$$
Using these new variables, we ﬁnd that the posterior may be rewritten as a Gaussian distribution

$$
p(\boldsymbol{w} | \boldsymbol{X}, \boldsymbol{y}) \propto \exp \left(-\frac{1}{2}\left(\boldsymbol{w}-\boldsymbol{\mu}_{m}\right)^{\top} \boldsymbol{\Lambda}_{m}^{-1}\left(\boldsymbol{w}-\boldsymbol{\mu}_{m}\right)+\frac{1}{2} \boldsymbol{\mu}_{m}^{\top} \boldsymbol{\Lambda}_{m}^{-1} \boldsymbol{\mu}_{m}\right)
$$

$$
\propto \exp \left(-\frac{1}{2}\left(\boldsymbol{w}-\boldsymbol{\mu}_{m}\right)^{\top} \boldsymbol{\Lambda}_{m}^{-1}\left(\boldsymbol{w}-\boldsymbol{\mu}_{m}\right)\right)
$$

Bayesian estimate provides a covariance matrix, showing how likely all the diﬀerent values of $$w$$ are, rather than providing only the estimate $$\mu_{m}$$.

## MAP estimation
One common reason for desiring a point estimate is that most operations involving the Bayesian posterior for most interesting models are intractable, and a point estimate oﬀers a tractable approximation. Rather than simply returning to the maximum likelihood estimate, we can still gain some of the beneﬁt of the Bayesian approach by allowing the prior to inﬂuence the choice of the point estimate.
One rational way to do this is to choose the maximuma posteriori(MAP) point estimat. The MAP estimate chooses the point of maximal posterior probability (or maximal probability density in the more common case of continuous $$θ$$):
$$
\boldsymbol{\theta}_{\mathrm{MAP}}=\underset{\boldsymbol{\theta}}{\arg \max } p(\boldsymbol{\theta} | \boldsymbol{x})=\underset{\boldsymbol{\theta}}{\arg \max } \log p(\boldsymbol{x} | \boldsymbol{\theta})+\log p(\boldsymbol{\theta})
$$
on the right hand side,$$
\log p(\boldsymbol{x} | \boldsymbol{\theta})$$, that is, the standard log-likelihood term, and $$\log p(\theta)$$, corresponding to the prior distribution.

As an example, consider a linear regression model with a Gaussian prior on the weights $$w$$. If this prior is given by $$
\mathcal{N}\left(\boldsymbol{w} ; \mathbf{0}, \frac{1}{\lambda} \boldsymbol{I}^{2}\right)
$$, then the log-prior term is proportional to the familiar $$
\lambda w^{T} w
$$ weight decay penalty, plus a term that does not depend on $$w$$ and does not aﬀect the learning process. MAP Bayesian inference with a Gaussian prior on the weights thus corresponds to weight decay.

As with full Bayesian inference, MAP Bayesian inference has the advantage of leveraging information that is brought by the prior and cannot be found in the training data. This additional information helps to reduce the variance in the MAP point estimate (in comparison to the ML estimate). However, it does so at the price of increased bias. 

Many regularized estimation strategies, such as maximum likelihood learning regularized with weight decay, can be interpreted as making the MAP approximation to Bayesian inference.