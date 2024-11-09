---
title: "Nonlinear Dimensionality Reduction with UMAP"
excerpt: "Course project for MA4270 Data Modelling and Computation.<br/><img src='/images/mnist.png'>"
collection: portfolio
---

Links to the materials: [report](MA4270_UMAP_report.pdf) \| [GitHub repository](https://github.com/hanyang-hu/MA4270-UMAP).

This is my course project for [MA4270 Data Modelling and Computation](https://nusmods.com/courses/MA4270/data-modelling-and-computation), where I get to explore manifold learning (or nonlinear dimensionality reduction) for the first time. 

I found this topic interesting since the geometry of non-Euclidean parameter spaces often arises in robotics (e.g. [this paper](https://arxiv.org/abs/1910.04998)). In addition, I was wondering if I could borrow the force-directed graph drawing paradigm to "stretch and contract" the input data, making it more suitable for Gaussian processes with stationary kernels, similar to a paper on [input warping through Beta CDF](https://arxiv.org/abs/1402.0929). The Beta CDF approach does not seem enough promising to me at first glance, since it only "stretches and contracts" in an axis-aligned manner. However, it seems a bit non-trivial to make UMAP work in this scenario, and maybe [deep kernel learning](https://arxiv.org/abs/1511.02222) should already be sufficient (perhaps more powerful?) for the same purpose.

The method that I focused on in this project was [UMAP](https://arxiv.org/abs/1802.03426) and its [parametric version](https://arxiv.org/abs/2009.12981). Except from reading these papers, I learned most details and intuitions about this approach from going through the [official implementations](https://github.com/lmcinnes/umap) and the [official documents](https://umap-learn.readthedocs.io/en/latest/). My report has some add-ons to the explanation of the attractive and repulsive forces, e.g. how they behave as the probabilistic weight changes.

For some unknown reason, my implementation of parametric UMAP in PyTorch was about 10 times slower in training speed compared to the official implementation. Although the final result seems okay, the slower training speed was unexpected and something I hope to optimize in the future (if I plan to use it for my other projects, and if I have time...).
