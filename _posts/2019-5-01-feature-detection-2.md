---
layout: post
comments: true
mathjax: true
priority: 440000
title: “Feature detection 2”
tags: [Computer Vision]
img: nyc.jpeg
excerpt: Computer Vision
date: 2019-05-01 12:00:00
---
## Image transformation
1. Geometric: Rotation, Scale
2. Photometric: Intensity change

## Invariance and equivariance
We want corner locations to be invariant to photometric transformations and equivariant to geometric transformations
1. Invariance: image is transformed and corner locations do not change
2. Equavariance: if we have two transformed versions of the same image, features should be detected in corresponding locations

## Some operartion and their characteristic
1. Derivatives and window function are equivariant
2. Corner location is equivariant w.r.t. translation
3. Corner location is equivariant w.r.t. image rotation
4. Affine intensity change: Partially invariant to affine intensity change
5. Scaling is neither invariant nor equivariant to scaling


## Key idea to scale invariant
find scale that gives local maximum of f
1. in both position and scale
2. one definition of f: the Harris operator
3. Instead of computing f for larger and larger windows, we can implement using a fixed window size with a Gaussian pyramid

## Laplacian of Gaussian
<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/57145522-62654400-6d91-11e9-8832-6c7ed841c528.png" style="border:none;width:100%">
</div>
$$
\nabla^{2} g=\frac{\partial^{2} g}{\partial x^{2}}+\frac{\partial^{2} g}{\partial y^{2}}
$$

This is a "Blob" detector, the maxima and minima of LoG operator in space and scale are blobs in the image

## An alternative
DoG: Difference of Gaussians
<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/57145808-fc2cf100-6d91-11e9-8ae7-6a5f7e043c3e.png" style="border:none;width:500%">
</div>

$$
D o G=G(x, y, k \sigma)-G(x, y, \sigma)
$$
