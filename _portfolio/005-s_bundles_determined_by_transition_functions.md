---
title: "Uniqueness of Vector Bundles From a Collection of Transition Functions"
excerpt: "Are transition functions all you need to determine a vector bundle?<br/><img src='/images/bundle_transition_function.png'>"
collection: portfolio
---

**Reference.** Chapter 1.2 of [Wells] Raymond O. Wells, *Differential Analysis on Complex Manifolds*. Graduate Texts in Mathematics Vol. 65, Springer (2008).

We could construct vector bundles from a collection of transition functions:

Let \\(X\\) be an \\(\mathcal{S}\\)-manifold. Suppose \\(X\\) has an open covering \\(X = \cup_{\alpha \in I} U_\alpha\\) and whenever \\(U_\alpha \cap U_\beta \not= \emptyset\\), we have an \\(\mathcal{S}\\)-funcion
\\[g_{\alpha\beta}: U_\alpha \cap U_\beta \to \text{GL}(r, K)\\]
satisfying the following equalities: 

1. $$g_{\alpha\beta}(q) \circ g_{\beta\gamma}(q) \circ g_{\gamma\alpha}(q) = \mathbf{I}_r$$ for $$q \in U_\alpha \cap U_\beta \cap U_\gamma$$; 

2. $$g_{\alpha\alpha}(q) = \mathbf{I}_{r}$$ for $$q \in U_\alpha$$.

Consider the disjoint union \\(\tilde{E} = \sqcup_{\alpha \in I} U_\alpha \times K^r\\) equipped with the natural product topology and \\(\mathcal{S}\\)-structure. Define an equivalence relation on \\(\tilde{E}\\) such that for \\((x, v) \in U_\beta \times K^r\\) and \\((y, w) \in U_\alpha \times K^r\\), \\((x, v) \sim (y, w)\\) iff. \\(y = x\\) and \\(w = g_{\alpha\beta}(x)v\\). This is indeed an equivalence relation due to the constraints on the transition functions. 

We have a natural surjection \\(\text{CL}: \tilde{E} \to E\\) by sending \\((x, v)\\) to its equivalence class. We equip \\(E\\) with the quotient topology, then \\(\pi: E \to X\\) defined by \\(\pi(\text{CL}(x, v)) = x\\) is an \\(\mathcal{S}\\)-bundle.

This shows the **existence** of vector bundles given a collection of transition functions, and it is natural to ask whether such construction is **unique** (which is a question raised by my classmate in [MA5210 Differentiable Manifolds](https://nusmods.com/courses/MA5210/differentiable-manifolds)). Let \\(\pi_0: E_0 \to X\\) and \\(\pi_1: E_1 \to X\\) be \\(\mathcal{S}\\)-bundles with the same trivializing open cover \\(\\{U_\alpha\\}\\) and collection of transition functions \\(\\{g_{\alpha\beta}\\}\\), it turns out that they are indeed \\(\mathcal{S}\\)-bundle isomorphic. 

My approach was inspired by a remark in [Wells] (after Example 2.12) on how to construct a new section by putting together a collection of compatible sections defined on trivializing open sets. In general, this is the *philosophy* of sheaves.

Let \\(\phi_\alpha: \pi_0^{-1}(U_\alpha) \to U_\alpha \times K^r\\) and \\(\psi_\alpha: \pi_1^{-1}(U_\alpha) \to U_\alpha \times K^r\\) be local trivializations of \\(\pi_0\\) and \\(\pi_1\\) respectively. Define \\(f: E_0 \to E_1\\) such that 
\\[f(v) = (\psi_\alpha^{-1} \circ \phi_\alpha)(v) \in \pi_1^{-1}(U_\alpha) \subset E_1\\]
for all \\(v \in \pi_0^{-1}(U_\alpha)\\). We shall verify that \\(f\\) is well-defined. 

For all \\(v \in \pi_0^{-1}(U_\alpha) \cap \pi_0^{-1}(U_\beta) \not= \emptyset\\), let \\((x, w) = \phi_\beta(v)\\) where \\(v \in E_{0,x}\\) and \\(w \in K^r\\), then
\\[(\psi_\alpha\circ\psi_\beta^{-1})(\phi_\beta(v)) = (\psi_\alpha\circ\psi_\beta^{-1})(x, w) = (x, g_{\beta\alpha}(x)w) = \phi_\alpha(v).\\] 
Consequently, we have \\((\psi_\alpha^{-1} \circ \phi_\alpha)(v) = (\psi_\beta^{-1} \circ \phi_\beta)(v)\\), i.e. \\(f\\) is indeed well-defined. 

It is easy to verify that \\(f: E_0 \to E_1\\) is bijective (the inverse is \\(f^{-1}(v) = (\phi_\alpha^{-1}\circ \psi_\alpha)(v)\\) for \\(v \in \pi_1^{-1}(U_\alpha)\\)) and is an \\(\mathcal{S}\\)-isomorphism (since \\(\phi_\alpha, \psi_\alpha\\) are \\(\mathcal{S}\\)-isomorphisms). 

Furthermore, \\(f\\) is fibre-preserving and gives a \\(K\\)-linear isomorphism on each fibre. Specifically, for every \\(p \in U_\alpha \subset X\\), we have 
\\[f_p: E_{0,p} \xrightarrow{\phi_\alpha} \\{p\\}\times K^r \xrightarrow{\psi_\alpha^{-1}} E_{1,p}.\\]
Consequently, \\(f: E_0 \to E_1\\) is an \\(\mathcal{S}\\)-bundle isomorphism.

**Thoughts.** Can we connect this to the one-one correspondence between vector bundles and locally free sheaves? I guess no. Although the proof that every locally free sheaves gives us a vector bundle uses the sheaf maps to construct a collection of transition maps, we only need the existence instead of the uniqueness. 