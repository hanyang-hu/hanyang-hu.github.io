---
title: "Unstructured High-Dimensional Bayesian Optimization"
excerpt: "The report and presentation for my summer research project in 2024."
collection: portfolio
---

Links to the materials: [report](UROPS_REPORT_Hu_Hanyang_Jonathan_Scarlett.pdf), [slides](UROPS_SLIDE_Hu_Hanyang_Jonathan_Scarlett.pdf) and [GitHub repository](https://github.com/hanyang-hu/unstructured_highdim_bo).

This is a credit-bearing undergraduate research project (MA3288) under the supervision of Prof. Jonathan Scarlett during the summer of 2024. The focus was around [this paper](https://arxiv.org/abs/2402.02229) by Hvarfner et al. that proposed the **dimensionality-scaled lengthscale prior** (DSP) to counteract the increase in complexity due to increased dimensionality, which relates to the [hypercube line picking problem](https://mathworld.wolfram.com/HypercubeLinePicking.html): for two points uniformly sampled in the \\(D\\)-dimensional hypercube, the mean distance between them grows asymptotically as fast as \\(\sqrt{D}\\), and hence **data points tend to be less correlated in higher dimensions**. Therefore, the DSP approach simply **scales up** the expectation of the prior of lengthscales by a factor of \\(\sqrt{D}\\), which could also be interpreted as imposing a low-complexity assumption on the objective functions, without using any structural assumptions (e.g., locality, low-dimensional active subspaces, etc.). 

The following figure shows the connection between lengthscale and model complexity from Hvarfner et al.:



What I did during the summer was trying to improve upon DSP by considering the **unknown hyperparameter issue** for Bayesian optimization in high-dimensional settings. A common technique in the low-dimensional setting is to gradually shrink lengthscales (i.e., gradually making harder assumptions) to avoid underfitting, such as [AR cool down](https://arxiv.org/abs/1612.03117) or [A-GP-UCB](https://jmlr.org/papers/v20/18-213.html). It seems that DSP is even more prone to underfitting since it makes a low-complexity assumption through the prior, hence lengthscale cool-down strategies might be worth trying. 

[TODO: describe difference between low and high dimensions]

[TODO: describe soft Winsorization]

[TODO: write about my recent thinkings on the hypercube line thinking problem]