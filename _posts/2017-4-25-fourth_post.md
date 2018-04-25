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
&= \text{Tr}\Big((\mathbf{P\Lambda^2 P}^\top)^{\tfrac{1}{2}}\Big) && \text{Orthogonality of } \mathbf{P} \\
&= \text{Tr}\Big(\mathbf{P}(\mathbf{\Lambda}^2)^{\tfrac{1}{2}}\mathbf{P}^\top \Big) && \text{square root of an eigendecomposition} \\
&= \text{Tr}\Big((\mathbf{\Lambda}^2)^{\tfrac{1}{2}} \Big) && \text{Trace of a PSD matrix} \\
&= \text{Tr}\Big( |\mathbf{\Lambda}| \Big) \\
&= ||\mathbf{\lambda}||_1 && \mathbf{\Lambda}=\text{diag}(\lambda)
\end{align*}
$$

### Note: square root of an eigendecomposition

Given an eigendecomposition $ \mathbf{Z} = \mathbf{P\Lambda P}^\top $, then the following holds:

$$ \Big(\mathbf{P\Lambda P}^\top \Big)^\frac{1}{2} = \mathbf{P\Lambda}^{\frac{1}{2}} \mathbf{P}^\top $$

Indeed:

\begin{align*}
  \Big( \mathbf{P\Lambda}^{\frac{1}{2}} \mathbf{P}^\top \Big)^2 &= \mathbf{P\Lambda}^{\frac{1}{2}} \mathbf{P}^\top \mathbf{P\Lambda}^{\frac{1}{2}} \mathbf{P}^\top \\
    &= \mathbf{P\Lambda}^{\frac{1}{2}} \mathbf{\Lambda}^{\frac{1}{2}} \mathbf{P}^\top \\
    &= \mathbf{P\Lambda P}^\top
\end{align*}

Now, take the square root and it's done!
