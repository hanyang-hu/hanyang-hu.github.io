---
title: "Uniqueness of Vector Bundles From a Collection of Transition Maps"
excerpt: "Regarding a question from a classmate in MA5210 Differential Manifolds.<br/><img src='/images/bundle_transition_function.png'>"
collection: portfolio
---

**Reference.** Chapter 1.2 of [Wells] Raymond O. Wells, *Differential Analysis on Complex Manifolds*. Graduate Texts in Mathematics Vol. 65, Springer (2008).

Let \\(X\\) be an \\(\mathcal{S}\\)-manifold. Suppose \\(X\\) has an open covering \\(X = \cup_{\alpha \in I} U_\alpha\\) and whenever \\(U_\alpha \cap U_\beta \not= \emptyset\\), we have an \\(\mathcal{S}\\)-funcion
\\[g_{\alpha\beta}: U_\alpha \cap U_\beta \to \text{GL}(r, K)\\]
satisfying the following equalities:

* $g_{\alpha\beta} \circ g_{\beta\gamma} \circ g_{\gamma\alpha} = \mathbf{I}_r$ on $U_\alpha \cap U_\beta \cap U_\gamma$ (in terms of matrix multiplication);

* $g_{\alpha\alpha} = \mathbf{I}_r$ on $U_\alpha$.

Inspired by Remark (c) right after Example 2.12 in [Wells] on how to construct a new section by putting together a collection of compatible sections defined on trivializing open sets, I came up with a way to define an \\(\mathcal{S}\\)-bundle isomorphism between \\(E_0\\) and \\(E_1\\) by putting together a collection of compatible \\(\mathcal{S}\\)-bundle isomorphisms.