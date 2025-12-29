---
title: "Forced Variational Integrator Networks with Unit Quaternions"
excerpt: "Course project for MATH 273A Geometric Numerical Integration.<br/><img src='/images/discrete_hamiltonian_map.png'>"
collection: portfolio
---

Links to the materials: [report](MATH273A_LieFVIN_report.pdf) \| [slides](MATH273A_LieFVIN_slides.pdf) \| [GitHub repository](https://github.com/hanyang-hu/S3FVIN).


Deep learning models excel in modeling complex dynamics, but incorporating physical priors like conservation laws can enhance their performance, especially in data-scarce regimes. Variational Integrator Networks (VINs) address this by embedding geometric numerical integrators into neural architectures. Here is a very simple example, consider a parameterized Lagrangian that characterizes a separable Newtonian system
\\[
    L_\theta(q, \dot{q}) = \frac{1}{2} \dot{q}^\top M_\theta \dot{q} - U_\theta(q)
\\]
the Velocity-Verlet method gives an explicit update rule

$$
\displaylines{
    q_{k+1} = q_k + h \dot{q}_k - \frac{h^2}{2} M_\theta^{-1} \nabla U_\theta(q_k) \\\
    \dot{q}_{k+1} = \dot{q}_k - h M_\theta^{-1} \left( \frac{\nabla U_\theta(q_k) + \nabla U_\theta(q_{k+1})}{2} \right)
}
$$

which can be interpreted as a feed-forward architecture. Similarly, we can also define parameterized Lagrangians on other manifolds (e.g., matrix Lie groups) and treat the corresponding variational integrator as a feed-forward architecture. 

In control applications like robotics, external forces (e.g., damping, control inputs) are crucial. Forced Variational Integrator Networks (FVINs) extend VINs to model these external forces (potentially given additional control inputs). I have put some detailed discussions on the discrete variational mechanics with forces in the report, trying to strengthen my theoretical understanding of this topic. One key takeaway is that forced variational integrators are generally neither symplectic nor momentum preserving. Nevertheless, they remain highly valuable in practice: for systems whose dynamics are predominantly governed by underlying physical laws, explicitly encoding this structure provides strong inductive bias and robustness, even in the presence of small external perturbations or modeling errors.


The coding part of this project attempts to extend LieFVINs from $$\text{SE}(3)$$ to the unit quaternion group $$S^3$$ for rigid body dynamics, hoping the less redundant representation of unit quaternions could be beneficial from the machine learning perspective. A key challenge with unit quaternions is their **double-covering issue** ($$q$$ and $$-q$$ represent the same rotation). To address this, I introduce a **sign-invariance trick** that enforces physically equivalent predictions under quaternion sign flips. This modification significantly improves training stability and leads to approximately $$2\times$$ faster convergence on a planar pendulum task. 

Beyond the quaternion-based model, I also found a simple improvement to the original $$\text{SO}(3)$$-based LieFVIN by adding an internal rotation-to-quaternion conversion $$R \mapsto q$$. This simple design retains the computational efficiency of $$\text{SO}(3)$$-based LieFVIN (where the implicit update admits a closed-form Jacobian via the Cayley transform) while benefiting from the less redundant quaternion representation during learning.