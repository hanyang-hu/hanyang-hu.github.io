---
title: "Neural ODE and Optimal Control"
excerpt: "Course project for MA5232 Modeling and Numerical Simulations.<br/><img src='/images/neural_ode_init.png'>"
collection: portfolio
---

**Reference.** Prof. Li Qianxiao's [lecture notes on optimal control](https://bpb-us-w2.wpmucdn.com/blog.nus.edu.sg/dist/5/11890/files/2021/10/LectureNotes_MA5232_2021.pdf).

Links to the materials: [GitHub repository](https://github.com/hanyang-hu/neural-ode-exp).

## Formulation of Optimal Control Problems

Consider the ODE $$\dot{x}(t) = f(t, x(t), u(t))$$ for $$t \in [t_0, t_1]$$ and $$x(t_0) = x_0$$. 

The Bolza problem with a Lagrangian $$L(t, x(t), u(t))$$ and a terminal cost function $$\Phi(t_1, x(t_1))$$ is stated below

$$
\displaylines{
    \inf_{u} J[u] = \int_{t_0}^{t_1} L(t, x(t), u(t))dt + \Phi(t_1, x(t_1)) \\\
    \text{ s.t. } \quad \dot{x}(t) = f(t, x(t), u(t)) \quad \text{ and } \quad x(t_0) = x_0.
}
$$

## Pontryagin’s Maximum Principle

By the **Pontryagin’s maximum principle (PMP)**, the optimal control $u^\ast$ and the corresponding state trajectory $x^\ast$ and co-state trajectory $p^\ast$ must satisfy

$$
\displaylines{
    \dot{x}^\ast(t) = \nabla_p H(t, x^\ast(t), p^\ast(t), u^\ast(t)), \qquad x^\ast(0)=x_0 \\\
    \dot{p}^\ast(t) =-\nabla_x H(t, x^\ast(t), p^\ast(t), u^\ast(t)), \qquad p^\ast(T)=-\nabla_x\Phi(x^\ast(T)) \\\
    H(t, x^\ast(t), p^\ast(t), u^\ast(t)) \geq H(t, x^\ast(t), p^\ast(t), u) \qquad (\text{for all $u \in \mathbb{R}^m$ and a.e. $t \in [0, T]$}).
}
$$

where the Hamiltonian $$H(t,x, p, u) = p^\top f(t, x, u) - L(t, x, u)$$. 

The **method of successive approximations (MSA)** is based on the PMP.
<p align="center">
<img src='msa.png' alt='MSA Algorithm'>
</p>
**Remark.** We use $$u(\cdot)$$ to denote the control instead of $$\theta(\cdot)$$ as in the above algorithm. We will use $$u(\cdot)$$ unless otherwise mentioned.

Moreover, the update step 
\\[
    u(t) \gets \text{argmax}_u H(t, x(t), p(t), u)
\\] 
could be replaced by some gradient update rule 
\\[
    u_t \gets u_t + \eta \cdot \nabla_u H(t, x_t, p_t, u_t)
\\]
according to the following improvement guarantee (subject to some mild assumptions)

$$
\displaylines{
    J[u^\prime] - J[u] \leq -(S[u^\prime] - S[u]) + C \int_{t_0}^{t_1} \|u_t^\prime - u_t\|^2 dt
}
$$

where $$x_t$$ and $$p_t$$ are computed based on $$u$$ and $$S[u] = \int_{t_0}^{t_1} H(t, x_t, p_t, u^\prime)dt$$. That is to say, as long as we could increase the integral of the Hamiltonian $$S[u]$$ by some small perturbations, we are guaranteed to find better controls. 

## Neural ODE

We attempt to interpret the **adjoint sensitivities algorithm** used in [Neural ODEs](https://arxiv.org/abs/1806.07366) in the context of optimal control. In particular, consider the Mayer problem for simplicity

$$
\displaylines{
    \inf_u J[u] = \Phi(x(t_1)) \\\
    \text{ s.t. } \qquad \dot{x}(t) = f(t, x(t), u(t)), \qquad t \in [t_0, t_1], \qquad x(t_0) = x_0.
}
$$

Assume that the control $$u_\theta(\cdot)$$ is parameterized by $$\theta \in \Theta$$ in the parameter space $$\Theta$$ (e.g., $$u_\theta(\cdot)$$ is an MLP), then we could re-formulate the original problem as

$$
\displaylines{
    \inf_{\theta \in \Theta} J(\theta) = \Phi(x(t_1)) \\\
    \text{ s.t. } \qquad \dot{x}(t) = g(t, x(t), \theta(t)), \qquad t \in [t_0, t_1], \qquad x(t_0) = x_0.
}
$$

where $$\theta(t) \equiv \theta$$ is a constant and $$g(t, x(t), \theta(t)) = f(t, x(t), u_\theta(t))$$. By the adjoint sensitivities algorithm, the following gradient update rule with learning rate $$\eta$$ should be applied

$$
\displaylines{
    \theta_{\text{next}} \gets \theta + \eta \nabla_\theta J(\theta) 
}
$$

where

$$
\displaylines{
    \nabla_\theta J(\theta) = \int_{t_0}^{t_1}p(t)^\top \nabla_\theta g(t, x(t), \theta)dt \\\
    \text{ s.t.} \qquad \dot{x}(t) = \nabla_p H(t, x(t), p(t), \theta), \qquad x(0)=x_0 \\\
    \qquad \qquad \dot{p}(t) =-\nabla_x H(t, x(t), p(t), \theta), \qquad p(T)=-\nabla_x\Phi(x(T))
}
$$

with $$H(t, x, p, \theta) = p^\top g(t, x, \theta)$$. We may observe that this is equivalent to a variant of the MSA using the steepest ascent.

By the PMP, the optimal parameter $$\theta^\ast$$ satisfies

$$
\displaylines{
    \dot{x}^\ast(t) = \nabla_p H(t, x^\ast(t), p^\ast(t), \theta^\ast), \qquad x^\ast(0)=x_0 \\\
    \dot{p}^\ast(t) =-\nabla_x H(t, x^\ast(t), p^\ast(t), \theta^\ast), \qquad p^\ast(T)=-\nabla_x\Phi(x^\ast(T)) \\\
    H(t, x^\ast(t), p^\ast(t), \theta^\ast) \geq H(t, x^\ast(t), p^\ast(t), \theta) \qquad (\text{for all $\theta \in \Theta$ and a.e. $t \in [0, T]$}).
}
$$

Notice that the last condition necessarily requires

$$
\displaylines{
    \int_{t_0}^{t_1}H(t, x^\ast(t), p^\ast(t), \theta^\ast)dt \geq \int_{t_0}^{t_1}H(t, x^\ast(t), p^\ast(t), \theta)dt \qquad (\text{for all } \theta \in \Theta).
}
$$

Some intuitions behind our focus on this relaxed condition could be: 

1. From the improvement guarantee aforementioned, we only need to locally optimize the integral of the Hamiltonian; 

2. Assuming we are using the MSA to solve this converted problem, since we want the neural network parameters to be a constant, we could average the updated parameters at different timesteps, which results in an integral. 

Consequently, t makes sense to use the following gradient update rule with learning rate $$\eta$$

$$
\displaylines{
    \theta_{\text{next}} \gets \theta + \eta\nabla_\theta \int_{t_0}^{t_1}H(t, x(t), p(t), \theta)dt.
}
$$

Interchanging the integration and differentiation, it is easy to verify that this is equivalent to the gradient update rule in the adjoint sensitivites algorithms. 

## Hamilton-Jacobi-Bellman Equation

The PMP is a necessary condition for optimal control, a sufficient condition could be the **Hamilton-Jacobi-Bellman (HJB) equations**. It is similar to the Bellman equations in the context of reinforcement learning. Firstly, we need to define the value function

$$
\displaylines{
V(s, z) = \inf_{u} \int_{s}^{t_1} L(t, x(t), \theta(t))dt + \Phi(x(t_1)) \\\
\text{ s.t. } \quad \dot{x}(t) = f(t, x(t), u(t)) \quad \text{ and } \quad x(s) = z.
}
$$

That is to say, the value function $$V(s, z)$$ is the minimum cost attainable starting from the state $$z$$ at time $$t$$. 

The HJB equation is given by
\\[
\partial_t V(t, x) + \inf_{u}\{L(t, x, u) + [\nabla_x V(t, x)]^\top f(t, x, u)\} \quad \text{ s.t. } \quad V(t_1, x) = \Phi(x).
\\]
Some connections could be observed between the PMP and the HJB equations. For example, we could consider the co-state as $$p = -\nabla_x V(t, x)$$ (this is the **adjoint** mentioned in the [Neural ODE](https://arxiv.org/abs/1806.07366) paper).

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
Therefore, we could obtain a baseline by finding a numerical solution of the above RDE.

## A Simple Application

[...]