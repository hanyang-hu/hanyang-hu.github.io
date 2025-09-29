---
title: "Efficient Gaussian Processes for Model-based Planning"
excerpt: "Year-Long Mathematics Capstone Project at NUS.<br/><img src='/images/rl_env_overview.png'>"
collection: portfolio
---

Links to the materials: [report](FYP_REPORT_Hu_Hanyang_Jonathan_Scarlett.pdf) \| [slides](FYP_PRESENTATION_Hu_Hanyang_Jonathan_Scarlett.pdf) \| [GitHub repository](https://github.com/hanyang-hu/gp-mbrl).

## Summary

**How can robots learn faster and make better decisions with less trial-and-error?** That was the central question behind my final-year project at NUS, supervised by Prof. Jonathan Scarlett.

Model-based reinforcement learning (MBRL) agents typically rely on deep neural networks to model the dynamics of their environments. Compared to model-free RL, MBRL is often more sample-efficient because it explicitly learns a model of the environment, allowing the agent to plan and evaluate actions before executing them. These neural network models are powerful but can still be sample-hungry and prone to bias. Gaussian Processes (GPs), on the other hand, are flexible, uncertainty-aware models that often shine in low-data regimes. Nevertheless, GPs are notoriously expensive to scale, especially in high-dimensional, real-time planning tasks. My project aimed to explore ways to make GPs practical in these settings.

## Key Ideas and Tricks

1. **Integration with TD-MPC**: Mitigating the computational bottleneck of GP-based planning by requiring fewer model inferences in the iterative trajectory rollouts, as [TD-MPC](https://arxiv.org/abs/2203.04955) does.

2. **GP-based correction of MLP dynamics**: Using the MLP as a base model and training GPs to correct the MLP predictions (possibly in parallel). This hybrid approach potentially outperforms standalone GPs and uncorrected MLPs.

3. **Decoupled training-inference pipeline**: Separating offline hyperparameter optimization from online inference to minimize the training overhead of stochastic variational Gaussian processes (SVGPs) while preserving constant-time inference.

4. **Integration with DKL**: Balancing computational tractability and data efficiency by combining MLP feature extractors with GP modeling. DKL has the potential in mitigating the curse of dimensionality and enhancing the expressive power of standard kernels.

## Some Findings

1. GP-corrected models often matched or outperformed the TD-MPC baseline in standard control tasks, with a small amount of overhead (although it should be parallelizable).

2. Matérn kernels helped in environments with abrupt, contact-driven dynamics. My understanding is that the spectral density associated with Matérn kernels made it less sensitive to "high-frequency noises" caused by contacts.

3. DKL typically requires fewer inducing points to achieve the error tolerance (as it facilitates a more structured and data-adaptive feature representation), but sometimes at the cost of additional overhead and sample efficiency.

4. Scaling to more complex environments with higher dimensionality remains challenging. I have explored other approaches, such as local kernel interpolation and a method I refer to as "dynamical local projection", although these remain at a preliminary stage.

## Reflections

By the end of the project, I gained a clearer understanding of Gaussian Processes, dispelling some of their mystique. They are simply an alternative to neural networks, to be used judiciously depending on the problem. While methods like GP regression were known to be computationally heavy and prone to the curse of dimensionality, with modern algorithmic and hardware advancements, it is worth revisiting these classical yet elegant approaches and exploring how they can be combined with contemporary methods to achieve improved performance.

