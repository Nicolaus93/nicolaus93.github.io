---
layout: post
title: Member eigenvalues?
---

Given a real symmetric matrix $H \in \mathbb{R}^{n\times n}$, we can show that:

$$ \forall \mathbf{v} \in \mathbb{R}^n, \|\mathbf{v}\|=1 \Rightarrow \mathbf{v}^\top H \mathbf{v} \leq \lambda_{max} $$ 

where $\lambda_{max}$ denotes the largest eigenvalue of $H$.

## Proof

First of all, we use the eigendecomposition of a real symmetric value, e.g. $H = Q \Lambda Q^\top $ and introduce $\mathbf{y} = Q^\top \mathbf{v}$. Thus, we have:

$$
\begin{align*}
    \mathbf{v}^\top H \mathbf{v} & = \mathbf{y}^\top \Lambda 
    \mathbf{y} \\
    & = \lambda_{max} y_1^2 + \lambda_2 y_2^2 + \ldots + \lambda_n y_n^2  && \Lambda \text{ is a diagonal matrix} \\
    & \leq \lambda_{max} (y_1^2 + y_2^2 + \ldots + y_n^2) \\
    & = \lambda_{max} \mathbf{y}^\top \mathbf{y} \\
    & = \lambda_{max} \mathbf{v}^\top Q Q^\top \mathbf{v} \\
    & = \lambda_{max} \mathbf{v}^\top \mathbf{v} && Q Q^\top=I \\
    & = \lambda_{max} \|\mathbf{v} \|
\end{align*}
$$

Yayyy, we are done!
