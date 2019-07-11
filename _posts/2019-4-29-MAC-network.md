---
layout: post
comments: true
mathjax: true
priority: 440000
title: “MAC-Network --- Compositional Attention Networks For Machine Reasoning”
img: nlp.png
tags: [NLP,Computer Vision, Machine Learning]
excerpt: 
date: 2019-04-29 12:00:00
---
[Article](https://arxiv.org/pdf/1803.03067.pdf)<br>
[Code](https://github.com/acneyouth1996/mac-network-pytorch)
## Background:
This is a very interesting paper in VQA and I implemented this model in pytorch based on some other people's code to train on [NLVR](http://lic.nlp.cornell.edu/nlvr/) dataset, the original authors tensorflow implementation is a little bit redundant and hard to debug. Part of the code is from [rosinality](https://github.com/rosinality/mac-network-pytorch)
The reason why I think this model is awesome is that it does not require too much handcraft feature engineering so the model looks very straightforward and can be applied in many different senarios. 
<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/56935795-7c690300-6ac1-11e9-90bd-6d657f311dfa.png" style="border:none;width:80%">
</div>

## Intro:
The intuition of this model is from the classic computer architecture design, which composes of two major element, the control part and the memory part. The cell explicitly separates out memory from control, both represented recurrently, and one single cell consists of three operational units that work in tandem to perform a reasoning step: the control unit updates the control state to attend at each iteration to some aspect of a given question; the read unit extracts information out of a knowledge base, guided by the control and memory states; and the write unit integrates the retrieved information into the memory state, iteratively computing the answer. A high level overview of this model is in the below image.
<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/56935821-a6bac080-6ac1-11e9-8473-b654bcda78ef.png" style="border:none;width:80%">
</div>

## The model:
   * The input unit:
   The input unit transforms the raw inputs, in the case of VQA the raw inputs are questions and image given to the model into distributed vector representations. The image feature can be extracted by a pretrained resnet or other model but raw images would do fine for NLVR since images in this dataset is synthesis and cartoon like, so raw image is fine. The question is converted into a sequence of learned word embeddings and then processed by a bi-directional RNN, then by concatenate the last hidden state output of both direction, we could get a vector representation of the question., the question q is further transformed through a learned linear transformation into a position-aware vector $$\boldsymbol{q}_{i}=W_{i}^{d \times 2 d} q+b_{i}^{d}$$
   * The MAC cell:
   The MAC cell is a recurrent cell consists of three different unit, the control unit, the read unit and write unit.The control $$c_{i}$$ represents the reasoning operation the cell should accomplish in the ith step, selectively focusing on some aspect of the question. Concretely, it is represented by a soft attention-based weighted average of the question words $$cws; s = 1,...,S$$. The memory mi holds the intermediate result obtained from the reasoning process up to the ith step, computed recurrently by integrating the preceding hidden state $$m_{i−1}$$ with new information $$r_{i}$$ retrieved from the image, performing the ith reasoning operation $$c_{i}$$. 
     - The control unit: The control unit takes the responsibility of reasoning.It receives the contextual question words and the question position-aware representation and the control state from the preceding step ci−1 and consists of two stages:
        1. we combine $$q_{i}$$ and $$c_{i−1}$$ through a linear transformation 
        2. Subsequently, we cast cqi onto the space of the question words by comparing similarity between $$cq_{i}$$ and each question word.
        <div class="imgcap">
        <img src="https://user-images.githubusercontent.com/22668421/56935860-f4372d80-6ac1-11e9-860d-dd227720fed9.png"  style="border:none;width:80%">
        </div>
        
        ```python
        class ControlUnit(nn.Module):
            def __init__(self, dim, max_step):
                super().__init__()

                self.position_aware = nn.ModuleList()
                for i in range(max_step):
                self.position_aware.append(linear(dim * 2, dim))

                self.control_question = linear(dim * 2, dim)
                self.attn = linear(dim, 1)

                self.dim = dim

            def forward(self, step, context, question, control):
                position_aware = self.position_aware[step](question)

                control_question = torch.cat([control, position_aware], 1)
                control_question = self.control_question(control_question)
                control_question = control_question.unsqueeze(1)

                context_prod = control_question * context
                attn_weight = self.attn(context_prod)

                attn = F.softmax(attn_weight, 1)

                next_control = (attn * context).sum(1)

                return next_control
        ```
     - The Read Unit:For the ith step, the read unit (see figure 5) inspects the knowledge base (the image) and output retrieved  information ri by performing a two-stage attention process over the knowledge base elements. detailed attention calculation is as follow:
        1. Compute the direct interaction between the knowledge-base element $$k_{h,w}$$ and the memory $$m_{i−1}$$, resulting in $$I_{i,h,w}$$.
        2. Concatenate the element $$k_{h,w}$$ to $$I_{i,h,w}$$ and pass the result through a linear transformation, yielding $$I_{i,h,w}^{\prime}$$
        3. We measure the similarity of $$c_{i}$$ to each of the interactions $$I_{i,h,w}$$ and pass the result through a softmaxlayer. This produces an attention distribution over the knowledge base elements.
        <div class="imgcap">
        <img src="https://user-images.githubusercontent.com/22668421/59438561-bd8f4b00-8dc1-11e9-9b2b-ec80ef2d9b00.png"  style="border:none;width:80%">
        </div>
        
        ```python
        class ReadUnit(nn.Module):

            def __init__(self, dim):
                super().__init__()
                self.mem = linear(dim, dim)
                self.concat = linear(dim * 2, dim)
                self.attn = linear(dim, 1)

            def forward(self, memory, know, control):
                mem = self.mem(memory[-1]).unsqueeze(2)
                concat = self.concat(torch.cat([mem * know, know], 1) \
                                        .permute(0, 2, 1))
                attn = concat * control[-1].unsqueeze(1)
                attn = self.attn(attn).squeeze(2)
                attn = F.softmax(attn, 1).unsqueeze(1)

                read = (attn * know).sum(2)

                return read
        ```
     - The write unit: The write unit integrates the information retrieved from the read unit ri with the preceding intermediate result $$m_{i−1}$$, guided by the $$i$$th reasoning operation
        1. First, we combine the new information ri with the prior intermediate result $$m_{i−1}$$ by a linear transformation, resulting in $$m_{info}$$, there are two other optional steps, I did not put it here, but you can find in the original paper.
        <div class="imgcap">
        <img src="https://user-images.githubusercontent.com/22668421/59438600-d39d0b80-8dc1-11e9-9769-9bacf5d415c8.png"  style="border:none;width:80%">
        </div>
       
        ```python
        class WriteUnit(nn.Module):
            def __init__(self, dim, self_attention=False, memory_gate=False):
                super().__init__()

                self.concat = linear(dim * 2, dim)

                if self_attention:
                    self.attn = linear(dim, 1)
                    self.mem = linear(dim, dim)

                if memory_gate:
                    self.control = linear(dim, 1)

                self.self_attention = self_attention
                self.memory_gate = memory_gate

            def forward(self, memories, retrieved, controls):
                prev_mem = memories[-1]
                concat = self.concat(torch.cat([retrieved, prev_mem], 1))
                next_mem = concat

                if self.self_attention:
                    controls_cat = torch.stack(controls[:-1], 2)
                    attn = controls[-1].unsqueeze(2) * controls_cat
                    attn = self.attn(attn.permute(0, 2, 1))
                    attn = F.softmax(attn, 1).permute(0, 2, 1)

                    memories_cat = torch.stack(memories, 2)
                    attn_mem = (attn * memories_cat).sum(2)
                    next_mem = self.mem(attn_mem) + concat

                if self.memory_gate:
                    control = self.control(controls[-1])
                    gate = F.sigmoid(control)
                    next_mem = gate * prev_mem + (1 - gate) * next_mem

                return next_mem
        ```
    * The output unit: The output unit predicts the final answer to the question based on the question representation q and the final memory mp, which represents the final intermediate result of the reasoning process, holding relevant information from the knowledge base

## Results
The results can be found in authors paper, I trained on NLVR using raw image feature as well as image feature extracted by Resnet, using resnet, validation ACC reached 0.55 and 0.53 using raw image. I think the author has similar results on NLVR as well. The reason for the problem behind this is that the amount of data is not adequate.






     


