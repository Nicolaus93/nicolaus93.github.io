---
layout: post
title: Member nuclear norm?
---

## Nuclear norm of a symmetric matrix

For any symmetric matrix $\mathbf{Z} \in \mathbb{S}^{d\times d}$, the nuclear norm is defined as:

$$
\begin{align*} 
|| \mathbf{Z} ||_* &= \text{Tr}\Big((\mathbf{Z}^2)^{\frac{1}{2}}\Big) \\
&= \text{Tr}\Big((\mathbf{P\Lambda P}^\top\mathbf{P\Lambda P}^\top)^{\tfrac{1}{2}}\Big) && \text{Eigendecomposition of } \mathbf{Z} \\
&= \text{Tr}\Big((\mathbf{P\Lambda}^2 \mathbf{P}^\top)^{\tfrac{1}{2}}\Big) && \text{Orthogonality of } \mathbf{P} \\
&= \text{Tr}\Big(\mathbf{P}(\mathbf{\Lambda}^2)^{\tfrac{1}{2}}\mathbf{P}^\top \Big) && \text{square root of an eigendecomposition} \\
&= \text{Tr}\Big((\mathbf{\Lambda}^2)^{\tfrac{1}{2}} \Big) \\
&= \text{Tr}\Big( |\mathbf{\Lambda}| \Big) \\
&= ||\mathbf{\lambda}||_1 && \mathbf{\Lambda}=\text{diag}(\lambda)
\end{align*}
$$

## Note1: square root of an eigendecomposition

Given an eigendecomposition $ \mathbf{Z} = \mathbf{P\Lambda P}^\top $, then the following holds:

$$ \Big(\mathbf{P\Lambda P}^\top \Big)^\frac{1}{2} = \mathbf{P\Lambda}^{\frac{1}{2}} \mathbf{P}^\top $$

Indeed:

$$
\begin{align*}
  \Big( \mathbf{P\Lambda}^{\frac{1}{2}} \mathbf{P}^\top \Big)^2 &= \mathbf{P\Lambda}^{\frac{1}{2}} \mathbf{P}^\top \mathbf{P\Lambda}^{\frac{1}{2}} \mathbf{P}^\top \\
    &= \mathbf{P\Lambda}^{\frac{1}{2}} \mathbf{\Lambda}^{\frac{1}{2}} \mathbf{P}^\top \\
    &= \mathbf{P\Lambda P}^\top
\end{align*}
$$

Now, take the square root and it's done!

## Note2: trace properties

The matrices in a trace of a product can be switched without changing the result. If $\mathbf{A}$ is an $m \times n$ matrix and $\mathbf{B}$ is an $n \times m$ matrix, then the following holds:

$$ \text{Tr}(\mathbf{AB}) = \text{Tr}(\mathbf{BA}) $$

Then:

$$ 
\text{Tr}\Big(\mathbf{P}(\mathbf{\Lambda}^2)^{\tfrac{1}{2}}\mathbf{P}^\top \Big) = \text{Tr}\Big(\mathbf{P}\big((\mathbf{\Lambda}^2)^{\tfrac{1}{2}}\mathbf{P}^\top \big) \Big) = \text{Tr}\Big(\big((\mathbf{\Lambda}^2)^{\tfrac{1}{2}}\mathbf{P}^\top \big) \mathbf{P} \Big) =
\text{Tr}\Big((\mathbf{\Lambda}^2)^{\tfrac{1}{2}} \Big)
$$ 

## Info
[eigendecomposition](http://www.onmyphd.com/?p=eigen.decomposition)
