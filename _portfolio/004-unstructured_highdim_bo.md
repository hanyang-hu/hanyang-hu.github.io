---
title: "Unstructured High-Dimensional Bayesian Optimization"
excerpt: "Reflections on my summer undergraduate research project in 2024.<br/><img src='/images/eggholder_2d.png'>"
collection: portfolio
---

Links to the materials: [report](UROPS_REPORT_Hu_Hanyang_Jonathan_Scarlett.pdf) \| [slides](UROPS_SLIDE_Hu_Hanyang_Jonathan_Scarlett.pdf) \| [GitHub repository](https://github.com/hanyang-hu/unstructured_highdim_bo).


This is a credit-bearing undergraduate research project (MA3288) under the supervision of Prof. Jonathan Scarlett during the summer of 2024. The focus was around [this paper](https://arxiv.org/abs/2402.02229) by Hvarfner et al. that proposed the **dimensionality-scaled lengthscale prior** (DSP) to counteract the increase in complexity due to increased dimensionality, which relates to the [hypercube line picking problem](https://mathworld.wolfram.com/HypercubeLinePicking.html): for two points uniformly sampled in the \\(D\\)-dimensional hypercube, the mean distance between them grows asymptotically as fast as \\(\sqrt{D}\\), and hence **data points tend to be less correlated in higher dimensions**. Therefore, the DSP approach simply **scales up** the expectation of the prior of lengthscales by a factor of \\(\sqrt{D}\\), which could also be interpreted as imposing a low-complexity assumption on the objective functions, without using any structural assumptions (e.g., locality, low-dimensional active subspaces, etc.). 


The following figure from Hvarfner et al. shows the connection between lengthscale and model complexity:
![lengthscales and complexity](./low_complexity_exp.png)
<br />

What I did during the summer was trying to improve upon DSP by considering the **unknown hyperparameter issue** for Bayesian optimization in high-dimensional settings. A common technique in the low-dimensional setting is to gradually shrink lengthscales (i.e., gradually making harder assumptions) to avoid underfitting (as illustrated below by [Berkenkamp et al.](https://jmlr.org/papers/v20/18-213.html)). [AR cool down](https://arxiv.org/abs/1612.03117) and [A-GP-UCB](https://jmlr.org/papers/v20/18-213.html) are examples that worked in the low-dimensional setting. It seems that DSP is even more prone to this kind of underfitting since it makes a low-complexity assumption, hence lengthscale cool-down strategies might be worth trying. 
![underfitting with small lengthscale](./unknown_hyp.png)


One observation is that the lengthscale cooldown methods that worked for low-dimensional problems cannot be directly applied in the high-dimensional setting when we use **automatic relevance determination** (ARD), i.e. estimate different lengthscales for different dimensions. Both AR cool down and A-GP-UCB shrink lengthscales estimated initially, hence the relative importance of each dimension never changes. This is fine when there are not many dimensions in total. However, in high-dimensional setting, we would like to tell which dimension is more important, and which is basically inactive. Due to the **curse of dimensionality**, significantly more data points are required to give reasonable estimates of more lengthscale hyperparameters, and hence directly shrinking the initial lengthscale might not be a good idea:
![different in low and high dimensions](./unknown_hyp_high_dim.png)
Consequently, in this project, I was proposing and trying out some "meta-strategies" that shrink the prior/posterior of estimated lengthscales, combining with different lengthscales evolution strategies. 


Another approach that I tried out is to implicitly simplify the objective function. For this purpose, it may seem straightforward to lower the RKHS norm by multiplying the observations by a constant \\(0 < c < 1\\), but the practice in BoTorch is to standardize the observations, so this approach is not effective. What I thought of was to apply a sigmoid function to the observations, so that values far away from the observation mean are more pressed towards it. In fact, applying a sigmoid function to standardized data can be considered as a soft approximation to Winsorization, that's why I called this method **soft Winsorization**. It seems at least a bit useful for Gaussian process regression with a low-complexity assumption:
![soft winsorization](./low_comp_GP_softW.png)
<br />

Unfortunately, none of the methods aforementioned constantly outperform DSP. My initial conclusion when I shared my findings with Prof. Scarlett was that I had not done enough experiments. There might be a different set of hyperparameters that work best for problems of different dimensionality. However, when I came across the [UMAP](https://arxiv.org/abs/1802.03426) method for non-linear dimensionality reduction (for one ongoing course project), I learned that not only the mean distance grows with \\(\sqrt{D}\\), but the variance also converges to a constant (see [this answer on StackExchange](https://stats.stackexchange.com/questions/241504/central-limit-theorem-for-square-roots-of-sums-of-i-i-d-random-variables/242165#242165)). Therefore, scaling down the distance (i.e. scaling up lengthscales) by a factor of \\(\sqrt{D}\\) would make the variance converge to \\(0\\), and all points would be considered almost equally distant from others, which would be quite unreasonable. 

Then **why does vanilla BO work in high dimensions with DSP**? I believe most credit should be attributed to the **numerical stability** in estimating the lengthscale hyperparameters since the DSP provides additional gradients. In fact, it has been shown in the appendix of DSP's original paper that a stabilized MLE is already very competitive. The complexity-simplification aspect might only provide a marginal performance increase (maybe by avoiding locality as mentioned in the paper). On the other hand, larger lengthscales usually lead to easier optimization of the acquisition function (e.g. EI, UCB) as well, as discussed in the [elastic GP](https://proceedings.mlr.press/v70/rana17a.html) paper.