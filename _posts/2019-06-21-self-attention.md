---
layout: post
comments: true
mathjax: true
priority: 440000
title: “Self attetion”
tags: [NLP]
excerpt: NLP
date: 2019-06-21 12:00:00
---

This post is part of Jay Alammar's "[The Illustrated Transformer](http://jalammar.github.io/illustrated-transformer/)" blog, this blog is very well written, so I shamelessly stole it's self-sttention explanation part in here.

## Self-Attention at a High Level

Say the following sentence is an input sentence we want to translate:

”The animal didn't cross the street because it was too tired”

What does “it” in this sentence refer to? Is it referring to the street or to the animal? It’s a simple question to a human, but not as simple to an algorithm.

When the model is processing the word “it”, self-attention allows it to associate “it” with “animal”.

As the model processes each word (each position in the input sequence), self attention allows it to look at other positions in the input sequence for clues that can help lead to a better encoding for this word.


## Self-Attention in Detail

Let’s first look at how to calculate self-attention using vectors, then proceed to look at how it’s actually implemented – using matrices.

The **first step** in calculating self-attention is to create three vectors from each of the encoder’s input vectors (in this case, the embedding of each word). So for each word, we create a Query vector, a Key vector, and a Value vector. These vectors are created by multiplying the embedding by three matrices that we trained during the training process.

Notice that these new vectors are smaller in dimension than the embedding vector. Their dimensionality is 64, while the embedding and encoder input/output vectors have dimensionality of 512. They don’t HAVE to be smaller, this is an architecture choice to make the computation of multiheaded attention (mostly) constant.

<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59940115-d9c96280-9427-11e9-98dc-ba62bbedb0aa.png" style="border:none;width:100%">
</div>

What are the “query”, “key”, and “value” vectors? 

They’re abstractions that are useful for calculating and thinking about attention. Once you proceed with reading how attention is calculated below, you’ll know pretty much all you need to know about the role each of these vectors plays.

The **second** step in calculating self-attention is to calculate a score. Say we’re calculating the self-attention for the first word in this example, “Thinking”. We need to score each word of the input sentence against this word. The score determines how much focus to place on other parts of the input sentence as we encode a word at a certain position.

The score is calculated by taking the dot product of the query vector with the key vector of the respective word we’re scoring. So if we’re processing the self-attention for the word in position #1, the first score would be the dot product of q1 and k1. The second score would be the dot product of q1 and k2.

<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59940163-f796c780-9427-11e9-9e71-6cb6bb9f0409.png" style="border:none;width:100%">
</div>

The **third and forth steps** are to divide the scores by 8 (the square root of the dimension of the key vectors used in the paper – 64. This leads to having more stable gradients. There could be other possible values here, but this is the default), then pass the result through a softmax operation. Softmax normalizes the scores so they’re all positive and add up to 1.

<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59940218-22811b80-9428-11e9-8938-17fffcc4b86b.png" style="border:none;width:100%">
</div>

This softmax score determines how much how much each word will be expressed at this position. Clearly the word at this position will have the highest softmax score, but sometimes it’s useful to attend to another word that is relevant to the current word.

The **fifth step** is to multiply each value vector by the softmax score (in preparation to sum them up). The intuition here is to keep intact the values of the word(s) we want to focus on, and drown-out irrelevant words (by multiplying them by tiny numbers like 0.001, for example).

The **sixth step** is to sum up the weighted value vectors. This produces the output of the self-attention layer at this position (for the first word).

<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59940277-48a6bb80-9428-11e9-88a9-83280f8c6bbe.png" style="border:none;width:100%">
</div>

That concludes the self-attention calculation. The resulting vector is one we can send along to the feed-forward neural network. In the actual implementation, however, this calculation is done in matrix form for faster processing. 








