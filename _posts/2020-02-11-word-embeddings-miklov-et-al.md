---
title:  Miklov. et al - Distributed Representation Representations of Words and phrases and their compositionality.
date:   2020-02-11
layout: post
categories: reading-log
description: Engaging with the paper that brought us word2vec.
---

### Abstract 
The recently introduced continuous Skip-gram model is an efficient method for learning high-quality distributed vector representations that capture a large number of precise syntactic and semantic word relationships. In this paper we present several extensions that improve both the quality of the vectors and the training speed. By subsampling of the frequent words we obtain significant speedup and also learn more regular word representations. We also describe a simple alternative to the hierarchical softmax called negative sampling. 

An inherent limitation of word representations is their indifference to word order and their inability to represent idiomatic phrases. For example, the meanings of “Canada” and “Air” cannot be easily combined to obtain “Air Canada”. Motivated by this example, we present a simple method for finding phrases in text, and show that learning good vector representations for millions of phrases is possible.

### Outline:
Starts by noting that Skip Gram is a useful manner for efficiently learning high quality vector representations of words from large amounts of text data. The paper’s goal is to present improvements to existing limitations of skip gram, such as, it’s hard to figure out what Boston Globe means. 
Using a normal softmax function with this sort of task is silly because soft max is going to grow proportionally based on the size of your input. So, the paper improves it with a “hierarchical” softmax. 
"each word w can be reached by an appropriate path from the root of the tree.” Using a tree, we cut our work down to log2(W), a much needed improvement 
They also look at negative sampling. This is also used to deal with the large size of softmax output. 
They also subsample frequent words to speed up the learning process and make the data more helpful.
Negative Sampling outperformed Hierarchical softmax. Subsampling is quite helpful. 
“these representations exhibit linear structure that makes precise analogical reasoning possible”
words that occur in similar contexts to have similar embeddings: https://en.wikipedia.org/wiki/Word2vec
### Summary:
The inventors of word2vec provide a glimpse into the reasoning behind their programming choices and persuasively demonstrate a powerful use case for their new approach to utilizing word embeddings. 
