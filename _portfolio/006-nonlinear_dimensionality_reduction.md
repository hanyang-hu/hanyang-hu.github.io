---
title: "Nonlinear Dimensionality Reduction with UMAP"
excerpt: "Course project for MA4270 Data Modelling and Computation.<br/><img src='/images/mnist.png'>"
collection: portfolio
---

Links to the materials: [report](MA4270_UMAP_report.pdf) \| [GitHub repository](https://github.com/hanyang-hu/MA4270-UMAP).

This is my course project for [MA4270 Data Modelling and Computation](https://nusmods.com/courses/MA4270/data-modelling-and-computation), where I get to explore manifold learning (or nonlinear dimensionality reduction) for the first time. 

I found this topic interesting since the geometry of non-Euclidean parameter spaces often arises in robotics (e.g. [this paper](https://arxiv.org/abs/1910.04998)). In addition, I was wondering if I could borrow the force-directed graph drawing paradigm to "stretch and contract" the input data, making it more suitable for Gaussian processes with stationary kernels, similar to a paper on [input warping through Beta CDF](https://arxiv.org/abs/1402.0929). The Beta CDF approach does not seem enough promising to me at first glance, since it only "stretches and contracts" in an axis-aligned manner. However, it seems a bit non-trivial to make UMAP work in this scenario, and maybe [deep kernel learning](https://arxiv.org/abs/1511.02222) should already be sufficient (perhaps more powerful as well?) for the same purpose.

The method that I focused on in this project was [UMAP](https://arxiv.org/abs/1802.03426) and its [parametric version](https://arxiv.org/abs/2009.12981). Except from reading these papers, I learned most details and intuitions about this approach from going through the [official implementations](https://github.com/lmcinnes/umap) and the [official documents](https://umap-learn.readthedocs.io/en/latest/). My report has some add-ons to the explanation of the attractive and repulsive forces, e.g. how they behave as the probabilistic weight changes.

In addition, I also explored using probabilistic PCA on the nearest neighbors to approximate the tangent space (and hence estimate the dimension of the underlying manifold), it worked as expected (see the report if you are interested). It is based on the simple insight that the tangent space of an \\(n\\)-dimensional manifold is an \\(n\\)-dimensional vector space: Let $$\phi: U_\alpha \to \mathbb{R}^n$$ be a chart of the manifold \\(M\\), then \\(\phi\\) induces an isomorphism $$\phi^\ast: \mathcal{E}_{\mathbb{R}^n}(V') \to \mathcal{E}_M(\phi^{-1}(V'))$$ such that $$(\phi^\ast f)(x)=f(\phi(x))$$ for $$x\in\phi^{-1}(V')$$ and $$f \in \mathcal{E}_{\mathbb{R}^n}(V')$$, where \\(V'\\) is an open subset of \\(\phi(U)\\). Let \\(p \in U_\alpha\\) and take the direct limit, we have an isomorphism $$\phi_p^\ast: \mathcal{E}_{\mathbb{R}^n,\phi(p)}\to\mathcal{E}_{M, p}$$ between germs. Put \\(d\phi_p: T_p(M) \to T_{\phi(p)}(\mathbb{R}^n)\\) such that \\(d\phi_p(D) = D\circ \phi_p^\ast \in T_{\phi(p)}(\mathbb{R}^n)\\) for all \\(D \in T_p(M)\\), i.e. the following diagram commutes:
<p align="center">
<img src='tangent_cd.png' alt='Commutative Diagram' width='30%'>
</p>


Consequently, the Jacobian \\(d\phi_p: T_p(M) \to T_{\phi(p)}(\mathbb{R}^n)\\) is a linear isomorphism between the tangent spaces. It is known that 
<p align="center">
$$T_{\phi(p)}(\mathbb{R}^n) = \text{span}\left(\left\{\frac{\partial}{\partial x_1}, \ldots, \frac{\partial}{\partial x_n}\right\}\right)$$
</p>
is an \\(n\\)-dimensional vector space, hence the tangent space \\(T_p(M)\\) of the \\(n\\)-dimensional manifold \\(M\\) at point \\(p\\) is also \\(n\\)-dimensional.

**Note.** For some unknown reason, my implementation of parametric UMAP in PyTorch was about 10 times slower in training speed compared to the official implementation. Although the final result seems okay, the slower training speed was unexpected and something I hope to optimize in the future (if I plan to use it for my other projects, and if I have time...).
