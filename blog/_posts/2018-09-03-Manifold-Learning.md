---
title: "Manifold Learning"
subtitle: "t-NSE "
layout: post
tags : [machine-learning, manifold-learning, tsne, t-distributed Stochastic Neighbor Embedding, multidimensional-scaling]
fb-img: ../figs/2018-09-03-Manifold-Learning/cover.png
---

# Manifold Learning

## t-distributed Stochastic Neighbor Embedding.

t-SNE [1] is a tool to visualize high-dimensional data. It converts similarities between data points to joint probabilities and tries to minimize the Kullback-Leibler divergence between the joint probabilities of the low-dimensional embedding and the high-dimensional data. t-SNE has a cost function that is not convex, i.e. with different initializations we can get different results.

It is highly recommended to use another dimensionality reduction method (e.g. PCA for dense data or TruncatedSVD for sparse data) to reduce the number of dimensions to a reasonable amount (e.g. 50) if the number of features is very high. This will suppress some noise and speed up the computation of pairwise distances between samples. For more tips see Laurens van der Maaten’s FAQ [2].

# References

[1]: van der Maaten, L.J.P.; Hinton, G.E. Visualizing High-Dimensional Data
Using t-SNE. Journal of Machine Learning Research 9:2579-2605, 2008.
[2]: van der Maaten, L.J.P. t-Distributed Stochastic Neighbor Embedding
http://homepage.tudelft.nl/19j49/t-SNE.html
[3]: L.J.P. van der Maaten. Accelerating t-SNE using Tree-Based Algorithms.
Journal of Machine Learning Research 15(Oct):3221-3245, 2014. http://lvdmaaten.github.io/publications/papers/JMLR_2014.pdf



Continue ...
