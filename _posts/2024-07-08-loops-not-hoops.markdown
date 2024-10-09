---
layout: post
title:  "Loops not Hoops - Using For Loops to Understand High Dimensional Tensor Multiplication"
date:   2024-07-08 15:26:04 -0700
categories: AI Math
---

# Exploring AI Mechanistic Interpretability with For Loops

Recently, I’ve found myself diving into the world of AI Mechanistic Interpretability. These black-box tools—like ChatGPT—feel almost magical, and I can't help but wonder how they really work. How do we transform a seemingly abstract, mysterious AI into something we can understand and interpret? For me, it seems clear: if we're deploying AI models across industries at scale, we need to understand not just their outputs, but also the intermediate workings inside. It’s like looking under the hood to see what makes the engine purr before we put it on the racetrack.

## Understanding Transformer Calculations

When I started implementing transformers, I was often faced with intimidating tensor calculations like this:

$$
W Shape[num_attn_heads, d_model, d_head]

Residual stream x (batch, sequence, d_model)

Q = Wx + b (batch, sequence, num_attn_heads, d_head)
$$
At first glance, these high-dimensional tensor operations seemed completely out of reach. But after weeks of chipping away at it, something clicked: there's actually a pretty intuitive way to think about these calculations—using the concept of “for loops.”

## Breaking Down the Residual Stream

In the case of transformers, the residual stream is our incoming data. For simplicity, let’s say each token in the data is a word—like “ am”. As input, each token is represented by a vector of numbers, and the length of that vector is called “d_model.” From there, all the other dimensions in the tensor are basically loops, repeating this transformation step by step. You could imagine breaking down the calculation for Q = Wx + b as follows:

For each batch of data (e.g., a paragraph):

For each sequence of tokens in the batch (e.g., a sentence):

For each token (e.g., a word):

Perform some operation on the token vector.

And that’s essentially what’s happening with x!

## Understanding the Weight Matrix W

Now, let’s look at W. This part can feel more complex, but the “for loop” analogy still works. W is fundamentally a linear transformation that maps vectors of size “d_model” to vectors of size “d_head,” doing this for each attention head. The equation Q = Wx + b can be imagined like this:

For each batch of data (e.g., a paragraph):

For each sequence of tokens in the batch (e.g., a sentence):

For each token embedding vector in the sequence (e.g., a word):

For each attention head:

Map the vector x_vector (size d_model) to a vector of size d_head.

The result of this is that Q ends up with a shape of (batch, sequence, num_attn_heads, d_head). Using this nested “for loop” structure helps me keep the dimensions and relationships clear in my head. Underneath it all, we’re really doing a simple linear transformation, but we’re doing it many, many times.

## Why Use Tensors Instead of For Loops?

Why use tensors instead of “for loops” for real? Well, GPUs are incredibly efficient at computing products of high-dimensional tensors in parallel. For loops imply sequential calculations, which can be painfully slow. And considering we need to perform tons of matrix operations in each iteration of training—and do it for many iterations—using tensors is definitely the way to go. But conceptually, thinking in terms of nested loops helps me distinguish between the steps that are functional and procedural versus those that represent data or connections in the model.

## Summary: For Loops as a Mental Model

So, in short, using “for loops” as a mental model can make these complex tensor operations more approachable—a bridge from traditional procedural programming into the tensor world. It’s helped me not only understand the transformer algorithm but also figure out if my outputs had the right shape. I’ve heard it’s pretty common among ML engineers to spend time staring at the shape of tensors to get them align!

Next up, I’ll show how this “for loop” approach can also be useful for interpreting features in sparse autoencoders. Stay tuned!