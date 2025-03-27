---
title: "Neural ODE and Optimal Control"
excerpt: "Course project for MA5232 Modeling and Numerical Simulations.<br/><img src='/images/neural_ode_init.png'>"
collection: portfolio
---

**Reference.** Prof. Li Qianxiao's [lecture notes on optimal control](https://bpb-us-w2.wpmucdn.com/blog.nus.edu.sg/dist/5/11890/files/2021/10/LectureNotes_MA5232_2021.pdf).

Links to the materials: [report](MA5232_Assignment_2.pdf) \| [GitHub repository](https://github.com/hanyang-hu/neural-ode-exp).

## Formulation of Optimal Control Problems

Consider the ODE $$\dot{x}(t) = f(t, x(t), u(t))$$ for $$t \in [t_0, t_1]$$ and $$x(t_0) = x_0$$. 

The Bolza problem with a Lagrangian $$L(t, x(t), u(t))$$ and a terminal cost function $$\Phi(t_1, x(t_1))$$ is stated below
$$
\begin{aligned}
    &\inf_{u} J[u] = \int_{t_0}^{t_1} L(t, x(t), u(t))dt + \Phi(t_1, x(t_1))\\ 
    &\text{ s.t. } \quad \dot{x}(t) = f(t, x(t), u(t)) \quad \text{ and } \quad x(t_0) = x_0.
\end{aligned}
$$

## Pontryagin’s Maximum Principle and Neural ODE

By the **Pontryagin’s maximum principle (PMP)**, the optimal control $u^\ast$ and the corresponding state trajectory $x^\ast$ and co-state trajectory $p^\ast$ must satisfy
$$
\begin{aligned}
    &\dot{x}^\ast(t) = \nabla_p H(t, x^\ast(t), p^\ast(t), u^\ast(t)), \qquad x^\ast(0)=x_0\\
    &\dot{p}^\ast(t) =-\nabla_x H(t, x^\ast(t), p^\ast(t), u^\ast(t)), \qquad p^\ast(T)=-\nabla_x\Phi(x^\ast(T))\\
    &H(t, x^\ast(t), p^\ast(t), u^\ast(t)) \geq H(t, x^\ast(t), p^\ast(t), u) \qquad (\text{for all $u \in \mathbb{R}^m$ and a.e. $t \in [0, T]$).}
\end{aligned}
$$
where the Hamiltonian $$H(t,x, p, u) = p^\top f(t, x, u) - L(t, x, u)$$. 

[...]

## Hamilton-Jacobi-Bellman Equation

The PMP is a necessary condition for optimal control, a sufficient condition could be the **Hamilton-Jacobi-Bellman (HJB) equations**. It is similar to the Bellman equations in the context of reinforcement learning. Firstly, we need to define the value function
$$
\begin{aligned}
&V(s, z) = \inf_{u} \int_{s}^{t_1} L(t, x(t), \theta(t))dt + \Phi(x(t_1)) \\
&\text{ s.t. } \quad \dot{x}(t) = f(t, x(t), u(t)) \quad \text{ and } \quad x(s) = z.
\end{aligned}
$$
That is to say, the value function $$V(s, z)$$ is the minimum cost attainable starting from the state $$z$$ at time $$t$$. 

The HJB equation is given by
\\[
\partial_t V(t, x) + \inf_{u}\{L(t, x, u) + [\nabla_x V(t, x)]^\top f(t, x, u)\} \quad \text{ s.t. } \quad V(t_1, x) = \Phi(x).
\\]
Some connections could be observed between the PMP and the HJB equations. For example, we could consider the co-state as $$p = -\nabla_x V(t, x)$$ (this is the **adjoint$$ mentioned in the [Neural ODE paper](https://arxiv.org/abs/1806.07366)).

## Linear Quadratic Regulator (LQR)

Consider the following controlled linear dynamical system
\\[
    \dot{x}_t = A(t)x_t + B(t)u_t \qquad A(t) \in \mathbb{R}^{d\times d}, \quad B(t) \in \mathbb{R}^{d \times m}, \quad u_t \in \mathbb{R}^m, \quad t \in [0, T]
\\]
with the cost fuctional defined by 
\\[
    J[u] = x_T^\top M x_T + \int_0^T x_t^\top Q(t) x_t + u_t^\top R(t) u_t dt
\\]
where $$M \in \mathbb{R}^{d\times d}$$ is symmetric positive semi-definite and $$\{Q(t), R(t)\,:\,t \in [0, T]\}$$ are symmetric positive definite matrices.

It is easy to verify that, $$V(t, x) = x^\top P(t) x$$ where $$P(t)$$ satisfies the following RDE
\\[
    \dot{P}(t) = -P(t)A(t)-A^\top(t)P(t)-Q(t)+P(t)B(t)R^{-1}(t)B^\top(t)P(t), \quad P(T)=M.
\\]
satisfies the HJB equations for the LQR problem. Furthermore, the optimal control $$u^\ast$$ could be extracted by
\\[
    u^\ast(t) = -R^{-1}(t)B^\top(t)P(t)x^\ast(t).
\\]
Therefore, we could use the numerical solution of the above RDE as a baseline.

## A Simple Application

[...]