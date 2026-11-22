---
layout: post
title: "Regret Matching through OMD and FTRL"
---

{% raw %}

The Riemann hypothesis has probably been proved. Whatever, as someone else said on X, I've become comfortably numb. It would have been much of a bigger deal one year or even six months ago. Now? Not so much. However, the biggest takeaway for me is that with a relatively cheap subscription (I don't even have a Pro ChatGPT one), one can get pretty far. I feel that LLMs still need to be guided, but with the right questions they're for sure much faster than their meat proxy at finding connections and solving theoretical problems. They're also pretty good for learning new topics. I continued my exploration of the Regret Matching algorithm. In [*Regret Matching, with and without the Plus*](/the last post/), we derived regret guarantees for RM and RM+ directly from their updates. Neither algorithm asked for a learning rate. This follow-up explains how both arise from classical optimization methods like mirror descent and Follow-The-Regularized-Leader.  

## 1. The general OMD and FTRL updates

Before specializing to regret matching, consider online optimization on a nonempty closed convex set $$K\subseteq\mathbb R^n$$. An algorithm $$\mathcal{A}$$ chooses $$\boldsymbol{w}_t\in K$$, observes a convex loss function $$f_t$$, and incurs $$f_t(\boldsymbol{w}_t)$$. Let $$\boldsymbol{g}_t\in\partial f_t(\boldsymbol{w}_t)$$ be an observed subgradient. The following updates use the loss-minimization convention of Orabona [1, Sections 6.4 and 7.1, Algorithms 6.1 and 7.1](#ref-orabona). Assume the minimizers exist and the divergences below are well-defined.

For a differentiable strictly convex function $$h$$ on a domain containing the iterates, its Bregman divergence is

$$
D_h(\boldsymbol{u},\boldsymbol{v})
 =h(\boldsymbol{u})-h(\boldsymbol{v})
  -\langle \nabla h(\boldsymbol{v}),\boldsymbol{u}-\boldsymbol{v}\rangle.
$$

Given an initial point $$\boldsymbol{w}_1$$ and step sizes $$\eta_t>0$$, online mirror descent (OMD) updates by

<a id="eq-generalomd"></a>

$$
\boxed{ \boldsymbol{w}_{t+1}\in\operatorname*{arg\,min}_{\boldsymbol{w}\in K}
 \left\{\langle \boldsymbol{g}_t,\boldsymbol{w}\rangle
             +\frac{1}{\eta_t}D_h(\boldsymbol{w},\boldsymbol{w}_t)\right\}.}
\tag{1}
$$

It balances the current linearized loss against movement from the current iterate. For $$h(\boldsymbol{w})=\lVert \boldsymbol{w}\rVert_2^2/2$$, the divergence is $$\lVert \boldsymbol{w}-\boldsymbol{w}_t\rVert_2^2/2$$, and OMD is Euclidean projected subgradient descent:

$$
\boldsymbol{w}_{t+1}=\Pi_K(\boldsymbol{w}_t-\eta_t\boldsymbol{g}_t),
$$

where $$\Pi_K$$ denotes Euclidean projection onto $$K$$.

Given regularizers $$\psi_t$$, follow-the-regularized-leader (FTRL) with linearized losses chooses

<a id="eq-generalftrl"></a>

$$
\boxed{ \boldsymbol{w}_t\in\operatorname*{arg\,min}_{\boldsymbol{w}\in K}
 \left\{\psi_t(\boldsymbol{w})
             +\langle \sum_{s=1}^{t-1}\boldsymbol{g}_s,\boldsymbol{w}\rangle\right\}.}
\tag{2}
$$

The empty sum at $$t=1$$ is zero. A common fixed choice is $$\psi_t=h/\eta$$ for $$\eta>0$$; time-varying regularizers are allowed in the general formulation. OMD uses a divergence centered at its current iterate, whereas FTRL regularizes accumulated losses. After recalling the regret-matching states and feedback, we specialize both updates to linear auxiliary losses on the nonnegative orthant.

## 2. A recap of the state and the feedback

At round $$t$$, an algorithm $$\mathcal{A}$$ chooses $$\boldsymbol{x}_t\in\Delta_n=\{\boldsymbol{x}\in\mathbb R^n_{\ge0}: \sum_{i=1}^n x_i=1\}$$, observes the full loss vector $$\boldsymbol{\ell}_t\in[0,1]^n$$, and incurs $$\langle \boldsymbol{x}_t,\boldsymbol{\ell}_t\rangle$$. Define

$$
\boldsymbol{r}_t=\langle \boldsymbol{x}_t,\boldsymbol{\ell}_t\rangle\mathbf{1}-\boldsymbol{\ell}_t,
 \qquad \boldsymbol{R}_t=\sum_{s=1}^t\boldsymbol{r}_s.
$$

For a fixed competitor $$\boldsymbol{q}\in\Delta_n$$, regret is $$\operatorname{Reg}_T(\boldsymbol{q})=\langle \boldsymbol{q},\boldsymbol{R}_T\rangle$$.

Let $$\left[\boldsymbol{v}\right]_+$$ be the coordinatewise positive part, and let $$\mathcal N(\boldsymbol{v})=\boldsymbol{v}/\|\boldsymbol{v}\|_1$$ for a nonzero nonnegative vector. Use a fixed fallback distribution, such as the uniform distribution, at $$\boldsymbol{v}=\mathbf{0}$$. Starting from zero states, the two algorithms use

$$
\begin{aligned}
 \text{RM:}\quad&\boldsymbol{x}_t=\mathcal N(\left[\boldsymbol{R}_{t-1}\right]_+),
 &\boldsymbol{R}_t&=\boldsymbol{R}_{t-1}+\boldsymbol{r}_t,\\
 \text{RM+:}\quad&\boldsymbol{x}_t=\mathcal N(\boldsymbol{Q}_{t-1}),
 &\boldsymbol{Q}_t&=\left[\boldsymbol{Q}_{t-1}+\boldsymbol{r}_t\right]_+.
 \end{aligned}
$$

Note that each algorithm forms $$\boldsymbol{r}_t$$ using its own played distribution.

<a id="sec-orthantreduction"></a>

## 3. From the nonnegative orthant to the simplex

To analyze these algorithms using the OMD/FTRL framework, it is useful to introduce a reduction as the one described in Orabona [1, Section 12.1, pp. 192–193](#ref-orabona). The following reduction applies to any online algorithm $$\mathcal{B}$$ whose predictions lie in $$\mathbb R^n_{\ge0}$$ (the nonnegative orthant). On round $$t$$, $$\mathcal{B}$$ produces $$\boldsymbol{w}_t\ge\mathbf{0}$$. The simplex algorithm $$\mathcal{A}$$ plays

$$
\boldsymbol{x}_t=\begin{cases}
  \boldsymbol{w}_t/\|\boldsymbol{w}_t\|_1,&\boldsymbol{w}_t\ne\mathbf{0},\\
  \boldsymbol{x}^{\mathrm{fallback}},&\boldsymbol{w}_t=\mathbf{0},
 \end{cases}
$$

where $$\boldsymbol{x}^{\mathrm{fallback}}\in\Delta_n$$ is fixed. After observing $$\boldsymbol{\ell}_t$$, $$\mathcal{A}$$ forms

$$
a_t=\langle \boldsymbol{x}_t,\boldsymbol{\ell}_t\rangle,\qquad
 \boldsymbol{r}_t=a_t\mathbf{1}-\boldsymbol{\ell}_t,
$$

and supplies the linear loss $$L_t(\boldsymbol{w})=-\langle \boldsymbol{r}_t,\boldsymbol{w}\rangle$$ to $$\mathcal{B}$$. The observed $$\boldsymbol{r}_t$$ is held fixed in this auxiliary loss. Thus the orthant algorithm receives centered feedback rather than the original expert losses.

The normalization ensures $$\boldsymbol{w}_t=\|\boldsymbol{w}_t\|_1\boldsymbol{x}_t$$, including at $$\boldsymbol{w}_t=\mathbf{0}$$. Since

$$
\langle \boldsymbol{r}_t,\boldsymbol{x}_t\rangle
 =a_t\langle \mathbf{1},\boldsymbol{x}_t\rangle-\langle \boldsymbol{\ell}_t,\boldsymbol{x}_t\rangle
 =a_t-a_t=0,
$$

we also have $$\langle \boldsymbol{r}_t,\boldsymbol{w}_t\rangle=0$$. For any $$\boldsymbol{q}\in\Delta_n$$ and $$\alpha>0$$, the vector $$\boldsymbol{u}=\alpha\boldsymbol{q}$$ is an admissible orthant comparator, and

$$
\begin{aligned}
 \langle -\boldsymbol{r}_t,\boldsymbol{w}_t-\alpha\boldsymbol{q}\rangle
 &=-\underbrace{\langle \boldsymbol{r}_t,\boldsymbol{w}_t\rangle}_{0}
       +\alpha\langle \boldsymbol{r}_t,\boldsymbol{q}\rangle\\
 &=\alpha\bigl(a_t\langle \mathbf{1},\boldsymbol{q}\rangle
                         -\langle \boldsymbol{\ell}_t,\boldsymbol{q}\rangle\bigr)\\
 &=\alpha\bigl(\langle \boldsymbol{x}_t,\boldsymbol{\ell}_t\rangle
                         -\langle \boldsymbol{q},\boldsymbol{\ell}_t\rangle\bigr).
 \end{aligned}
$$

Summing gives the exact regret-transfer identity

<a id="eq-orthantreduction"></a>

$$
\sum_{t=1}^T\langle -\boldsymbol{r}_t,\boldsymbol{w}_t-\alpha\boldsymbol{q}\rangle
 =\alpha\operatorname{Reg}_T(\boldsymbol{q}).
\tag{3}
$$

Consequently, an auxiliary regret bound $$B_T(\boldsymbol{u})$$ valid for every $$\boldsymbol{u}\ge\mathbf{0}$$ yields

$$
\operatorname{Reg}_T(\boldsymbol{q})\le\inf_{\alpha>0}
                         \frac{B_T(\alpha\boldsymbol{q})}{\alpha}.
$$

The comparator scale $$\alpha$$ is chosen only in the analysis and does not change the algorithm.

## 4. Regret matching through FTRL

Instantiate the reduction in Section [3](#sec-orthantreduction) with FTRL on $$\mathbb R^n_{\ge0}$$ and the fixed quadratic regularizer $$\psi(\boldsymbol{w})=\lVert \boldsymbol{w}\rVert_2^2/(2\eta)$$, for any constant $$\eta>0$$. Holding the observed regret vectors fixed when optimizing gives

<a id="eq-orthantftrlrm"></a>

$$
\boldsymbol{w}_t
 =\operatorname*{arg\,min}_{\boldsymbol{w}\ge\mathbf{0}}
   \left\{\frac{\lVert \boldsymbol{w}\rVert_2^2}{2\eta}
                        -\langle \boldsymbol{R}_{t-1},\boldsymbol{w}\rangle\right\}
 =\eta\left[\boldsymbol{R}_{t-1}\right]_+.
\tag{4}
$$

The reduction normalizes this prediction, giving

$$
\boldsymbol{x}_t=\mathcal N(\boldsymbol{w}_t)
          =\mathcal N(\left[\boldsymbol{R}_{t-1}\right]_+),
$$

with the same fallback when all weights vanish. This is exactly RM. The common $$\eta$$ scales the auxiliary weights and cancels from the played distribution. Farina, Kroer, and Sandholm [2](#ref-farina) give this connection between regret matching and FTRL.

The constant-step-size specialization of Orabona's Corollary 7.6 [1, Section 7.2, p. 102](#ref-orabona) gives

$$
\sum_{t=1}^T\langle -\boldsymbol{r}_t,\boldsymbol{w}_t-\boldsymbol{u}\rangle
 \le\frac{\lVert \boldsymbol{u}\rVert_2^2}{2\eta}
       +\frac{\eta}{2}\sum_{t=1}^T\lVert \boldsymbol{r}_t\rVert_2^2,
 \qquad \boldsymbol{u}\in\mathbb R^n_{\ge0}.
$$

Here $$\lVert \boldsymbol{w}\rVert_2^2/2$$ is $$1$$-strongly convex in the Euclidean norm and has minimum zero on the orthant. Applying the regret-transfer identity [(3)](#eq-orthantreduction) with $$\boldsymbol{u}=\alpha\boldsymbol{q}$$ and dividing by $$\alpha>0$$ gives

$$
\operatorname{Reg}_T(\boldsymbol{q})
 \le\frac{\alpha\lVert \boldsymbol{q}\rVert_2^2}{2\eta}
       +\frac{\eta}{2\alpha}\sum_{t=1}^T\lVert \boldsymbol{r}_t\rVert_2^2.
$$

Minimizing over $$\alpha$$ gives

<a id="eq-rmftrlbound"></a>

$$
\boxed{\quad
 \operatorname{Reg}_T(\boldsymbol{q})\le\lVert \boldsymbol{q}\rVert_2
       \sqrt{\sum_{t=1}^T\lVert \boldsymbol{r}_t\rVert_2^2}
 \le\lVert \boldsymbol{q}\rVert_2\sqrt{nT}.
 \quad}
\tag{5}
$$

If all regret increments vanish, the bound holds directly. Otherwise the minimizer is $$\alpha=\eta\sqrt{\sum_t\lVert \boldsymbol{r}_t\rVert_2^2}/\lVert \boldsymbol{q}\rVert_2$$. This is a comparator choice in the analysis, not an algorithm parameter: the same run satisfies the bound simultaneously for every $$\boldsymbol{q}$$. The last inequality uses $$\lvert r_{t,i}\rvert\le1$$. Thus the standard FTRL guarantee and the orthant-to-simplex reduction recover the bound from the [first post](/regret-matching/) for every common fixed $$\eta>0$$.

## 5. Regret matching+ through OMD

Theorem 2 of Farina, Kroer, and Sandholm [2](#ref-farina) connects RM+ to Euclidean OMD. Form $$\boldsymbol{r}_t$$ from the distribution played by RM+ and hold it fixed when optimizing. Fix any constant $$\eta>0$$. Starting at $$\boldsymbol{w}_1=\mathbf{0}$$, the Euclidean OMD update on the nonnegative orthant is

<a id="eq-omd"></a>

$$
\begin{aligned}
\boldsymbol{w}_{t+1}
 &=\operatorname*{arg\,min}_{\boldsymbol{w}\ge\mathbf{0}}\left\{-\langle \boldsymbol{r}_t,\boldsymbol{w}\rangle
                       +\frac{\lVert \boldsymbol{w}-\boldsymbol{w}_t\rVert_2^2}{2\eta}\right\}\\
 &=\left[\boldsymbol{w}_t+\eta \boldsymbol{r}_t\right]_+.
\end{aligned}
\tag{6}
$$

Euclidean OMD directly gives, for every $$\boldsymbol{u}\in\mathbb R^n_{\ge0}$$,

$$
\sum_{t=1}^T\langle -\boldsymbol{r}_t,\boldsymbol{w}_t-\boldsymbol{u}\rangle
 \le\frac{\lVert \boldsymbol{u}\rVert_2^2}{2\eta}
       +\frac{\eta}{2}\sum_{t=1}^T\lVert \boldsymbol{r}_t\rVert_2^2.
$$

Applying the regret-transfer identity [(3)](#eq-orthantreduction) with $$\boldsymbol{u}=\alpha\boldsymbol{q}$$ gives

$$
\operatorname{Reg}_T(\boldsymbol{q})\le
 \frac{\alpha\lVert \boldsymbol{q}\rVert_2^2}{2\eta}
 +\frac{\eta}{2\alpha}\sum_{t=1}^T\lVert \boldsymbol{r}_t\rVert_2^2.
$$

Optimizing $$\alpha>0$$ recovers [(5)](#eq-rmftrlbound), now for RM+'s regret sequence. As for RM, normalization removes the common auxiliary learning rate, and comparator-scale optimization supplies the tuning-free bound.

## 6. Interpreting the regret bound

Let $$i^\star\in\operatorname*{arg\,min}_i\sum_{t=1}^T\ell_{t,i}$$ be a best expert in hindsight. For the comparator $$\boldsymbol{q}=\boldsymbol{e}_{i^\star}$$, $$\lVert \boldsymbol{q}\rVert_2=1$$, so [(5)](#eq-rmftrlbound) becomes

$$
\operatorname{Reg}_T(\boldsymbol{e}_{i^\star})
 \le\sqrt{\sum_{t=1}^T\lVert \boldsymbol{r}_t\rVert_2^2}
 =\sqrt{\sum_{t=1}^T\sum_{i=1}^n r_{t,i}^2}.
$$

The comparator norm disappears, but the squared regrets still sum over all experts. Comparing this expression with $$\sqrt{T\ln n}$$, without accounting for constants in particular algorithmic guarantees, it is smaller precisely when

$$
\frac1T\sum_{t=1}^T\lVert \boldsymbol{r}_t\rVert_2^2<\ln n.
$$

For example, if the expert losses have range at most $$\varepsilon$$ on every round, then $$\lvert r_{t,i}\rvert\le\varepsilon$$ and the bound is at most $$\varepsilon\sqrt{nT}$$. This improves on the benchmark when $$\varepsilon<\sqrt{\ln n/n}$$. Alternatively, if $$\lVert \boldsymbol{r}_t\rVert_2^2\le k$$ on every round, as can happen when few coordinates contribute appreciably, the bound is $$\sqrt{kT}$$ and is smaller when $$k<\ln n$$. In the worst case the squared norm can be of order $$n$$, giving the familiar $$\sqrt{nT}$$ bound.

Small actual regret does not necessarily make this upper bound small: negative regrets also contribute their squares. On a round where $$\mathcal{A}$$ assigns all probability to a zero-loss expert and every other expert has loss one, its regret to that expert is zero, but $$\lVert \boldsymbol{r}_t\rVert_2^2=n-1$$. Thus the second-order quantity measures squared differences from all experts, not just performance relative to the best one.

Finally, $$\sqrt{T\ln n}$$ is a worst-case benchmark. The polynomial variants of RM and RM+ also have a data-dependent guarantee of order

$$
\sqrt{\ln n\sum_{t=1}^T\|\boldsymbol{r}_t\|_\infty^2},
$$

so they benefit from small loss ranges as well. When comparing different algorithms, each bound must use the regret vectors generated by that algorithm's own predictions.

<a id="sec-dynamicregret"></a>

## 7. Dynamic regret without changing RM+

The comparator need not be fixed over time. Let $$\boldsymbol{q}_1,\ldots,\boldsymbol{q}_T\in\Delta_n$$ be any comparator sequence. In our loss convention, its dynamic regret and Euclidean path length are

$$
\operatorname{DReg}_T(\boldsymbol{q}_{1:T})
 =\sum_{t=1}^T\langle \boldsymbol{x}_t-\boldsymbol{q}_t,\boldsymbol{\ell}_t\rangle
 =\sum_{t=1}^T\langle \boldsymbol{q}_t,\boldsymbol{r}_t\rangle,\qquad
 P_T=\sum_{t=1}^{T-1}\lVert \boldsymbol{q}_{t+1}-\boldsymbol{q}_t\rVert_2.
$$

Ordinary RM need not have sublinear dynamic regret even when the comparator switches only once. Let $$T=2m$$ for an integer $$m\ge1$$, use two experts and a uniform initial fallback, and choose the loss sequence

$$
\boldsymbol{\ell}_t=\begin{cases}
 (0,1),&1\le t\le m,\\
 (1,0),&m<t\le2m.
 \end{cases}
$$

An algorithm $$\mathcal{A}$$ running RM incurs loss $$1/2$$ on the first round, after which $$\boldsymbol{R}_1=(1/2,-1/2)$$. It chooses expert 1 for the rest of the first block, giving

$$
\boldsymbol{R}_m=(1/2,-m+1/2).
$$

Before round $$m+j$$, for $$1\le j\le m$$, induction gives

$$
\boldsymbol{R}_{m+j-1}=(1/2,-m+j-1/2).
$$

The second coordinate is still negative, so $$\mathcal{A}$$ chooses expert 1 on every round of the second block as well, incurring loss one each time.

Choose $$\boldsymbol{q}_t=\boldsymbol{e}_1$$ in the first block and $$\boldsymbol{q}_t=\boldsymbol{e}_2$$ in the second. This comparator has zero loss and $$P_T=\lVert \boldsymbol{e}_2-\boldsymbol{e}_1\rVert_2=\sqrt{2}$$, whereas

$$
\operatorname{DReg}_T(\boldsymbol{q}_{1:T})=m+\frac12=\frac{T+1}{2}.
$$

The negative balance retained by RM delays adaptation for the entire second block; RM+ discards such negative balances.

By the way, it is easy to verify (aka one more prompt) that the same loss sequence also gives linear dynamic regret for ordinary exponential weights. Additional exploration can address this, as in Herbster and Warmuth's fixed-share algorithm.

We consider the ordinary Euclidean RM+ algorithm, with zero initial state and without restarting or averaging its predictions.

Use the Euclidean OMD representation from Section 5:

$$
\boldsymbol{w}_{t+1}=[\boldsymbol{w}_t+\eta\boldsymbol{r}_t]_+,
 \qquad \boldsymbol{w}_1=\mathbf{0},
$$

for any fixed $$\eta>0$$. For every $$\alpha>0$$, the moving comparator $$\boldsymbol{u}_t=\alpha\boldsymbol{q}_t$$ lies in the nonnegative orthant. The one-step projected-gradient inequality gives

$$
\langle -\boldsymbol{r}_t,\boldsymbol{w}_t-\alpha\boldsymbol{q}_t\rangle
 \le
 \frac{\lVert \boldsymbol{w}_t-\alpha\boldsymbol{q}_t\rVert_2^2
       -\lVert \boldsymbol{w}_{t+1}-\alpha\boldsymbol{q}_t\rVert_2^2}{2\eta}
 +\frac{\eta}{2}\lVert \boldsymbol{r}_t\rVert_2^2.
$$

Normalization and centering imply $$\langle \boldsymbol{w}_t,\boldsymbol{r}_t\rangle=0$$, including at the zero state, so the left-hand side is $$\alpha\langle \boldsymbol{q}_t,\boldsymbol{r}_t\rangle$$.

Write $$E_t=\sum_{s=1}^t\lVert \boldsymbol{r}_s\rVert_2^2$$. Expand the squared distances before summing: the $$\alpha^2\lVert \boldsymbol{q}_t\rVert_2^2$$ terms cancel within each round, and the squared iterate norms telescope. Summing by parts in the remaining inner products and dividing by $$\alpha$$ yields

$$
\begin{aligned}
\operatorname{DReg}_T(\boldsymbol{q}_{1:T})
 &\le \frac{1}{\eta}\langle \boldsymbol{q}_T,\boldsymbol{w}_{T+1}\rangle
   +\frac{1}{\eta}\sum_{t=1}^{T-1}
       \langle \boldsymbol{q}_t-\boldsymbol{q}_{t+1},\boldsymbol{w}_{t+1}\rangle\\
 &\quad+\frac{\eta^2E_T-\lVert \boldsymbol{w}_{T+1}\rVert_2^2}{2\eta\alpha}.
\end{aligned}
$$

This inequality holds for every $$\alpha>0$$ on the same algorithm run. Letting $$\alpha\to\infty$$ removes the last term. This changes only the auxiliary comparator in the analysis, not the OMD iterates or the played distributions. Thus

<a id="eq-dynamicomd"></a>

$$
\operatorname{DReg}_T(\boldsymbol{q}_{1:T})
 \le \frac{1}{\eta}\langle \boldsymbol{q}_T,\boldsymbol{w}_{T+1}\rangle
   +\frac{1}{\eta}\sum_{t=1}^{T-1}
       \langle \boldsymbol{q}_t-\boldsymbol{q}_{t+1},\boldsymbol{w}_{t+1}\rangle.
\tag{7}
$$

Keeping the terminal squared-distance term in the one-step inequality is essential to this cancellation.

Projection and centering also give

$$
\lVert \boldsymbol{w}_{t+1}\rVert_2^2
 \le\lVert \boldsymbol{w}_t+\eta\boldsymbol{r}_t\rVert_2^2
 =\lVert \boldsymbol{w}_t\rVert_2^2+\eta^2\lVert \boldsymbol{r}_t\rVert_2^2.
$$

Since $$\boldsymbol{w}_1=\mathbf{0}$$, induction gives $$\lVert \boldsymbol{w}_{t+1}\rVert_2\le\eta\sqrt{E_t}$$. Applying Cauchy–Schwarz to [(7)](#eq-dynamicomd) therefore yields

$$
\begin{aligned}
\operatorname{DReg}_T(\boldsymbol{q}_{1:T})
 &\le\lVert \boldsymbol{q}_T\rVert_2\sqrt{E_T}
   +\sum_{t=1}^{T-1}\sqrt{E_t}\lVert \boldsymbol{q}_{t+1}-\boldsymbol{q}_t\rVert_2\\
 &\le(1+P_T)\sqrt{E_T},
\end{aligned}
$$

where the last step uses $$\lVert \boldsymbol{q}_T\rVert_2\le1$$ and $$E_t\le E_T$$. The common step size $$\eta$$ has canceled from the bound, just as it cancels from the normalized predictions.

### The path-length guarantee

If $$\lVert \boldsymbol{r}_t\rVert_2\le G$$ for every round, then $$E_t\le G^2t$$, and hence

$$
\boxed{\displaystyle
 \operatorname{DReg}_T(\boldsymbol{q}_{1:T})
 \le G\sqrt{T}\,\lVert \boldsymbol{q}_T\rVert_2
    +G\sum_{t=1}^{T-1}\sqrt{t}\,\lVert \boldsymbol{q}_{t+1}-\boldsymbol{q}_t\rVert_2
 \le G\sqrt{T}(1+P_T).}
$$

Under our assumption $$\boldsymbol{\ell}_t\in[0,1]^n$$, one may take $$G=\sqrt{n}$$. The earlier bound in terms of $$E_t$$ can be sharper. When $$P_T=0$$, the comparator is fixed, and retaining $$\lVert \boldsymbol{q}_T\rVert_2$$ recovers the static bound.

These inequalities hold for every realized loss sequence and every comparator sequence, including one chosen after observing all losses. They require no stochastic or oblivious-adversary assumption. For fixed $$G$$, the displayed path-length bound is sublinear whenever $$P_T=o(\sqrt{T})$$. It does not guarantee sublinear regret for arbitrarily fast-changing comparators, and it does not claim optimal dependence on $$P_T$$: the factor $$1+P_T$$ is outside the square root. The algorithm itself is unchanged, and no output averaging is needed.

For a comparator that switches experts at most $$k$$ times, $$P_T\le\sqrt{2}\,k$$, so the bound gives dynamic regret at most $$(1+\sqrt{2}\,k)\sqrt{E_T}$$. With some work (aka one prompt), one can easily recover the exact constant in Theorem 2 of [3](#ref-houlimit), which gives $$(k+1)L\sqrt{nT}$$ for $$k$$ switches and per-round loss range at most $$L$$.

## 8. Discussion

Looking at the algorithms, FTRL accumulates the original increments before clipping; OMD projects after each increment. Negative balances survive in RM's history and are discarded in RM+'s state. That is exactly the distinction between the two algorithms. That's also intuitively why RM+ can adapt to switching comparators. The same derivations done by Farina et al. [2](#ref-farina) were also described in Flaspohler et al. [4, Section 4 and Lemma 8](#ref-flaspohler). They derive DORM and DORM+ from optimistic FTRL and OMD on the nonnegative orthant with delayed feedback. With no delay, zero hints, and a Euclidean regularizer, those constructions recover RM and RM+, respectively.

In general, ordinary regret matching is the $$p=2$$ case of the $$p$$-norm algorithms. The same choice recovers RM+ in the OMD construction. Choosing $$p=2\ln n$$ for $$n\ge3$$, where $$n$$ is the number of experts, gives the optimal worst-case regret order $$O(\sqrt{T\ln n})$$ for both constructions. We presented here the case with $$p=2$$ since it's the most widely known and gives rise to effective and practical algorithms. The common FTRL and OMD framework makes it easier to see where the updates come from and how to extend them. For example, Farina, Kroer, and Sandholm [2](#ref-farina) use it to develop predictive variants of both algorithms.

Several features may help explain the strong practical performance of RM+ [3](#ref-houlimit). Like RM, it needs no learning-rate tuning, is scale-free under a common positive rescaling of the losses, and is straightforward to implement. RM+ also tracks changing comparators with its usual update. Ordinary RM and exponential weights can incur linear regret after a single switch, as the example above shows (for exponential weights, this is fixed in its Fixed-Shared variant, which does it by mixing in additional exploration).

## References

<a id="ref-orabona"></a>

**\[1\]** Francesco Orabona. *Online Learning: A Modern Introduction Using Convex Optimization*. arXiv:1912.13213v10, 2026. Sections 6.4 and 7.1–7.3; Algorithms 6.1 and 7.1, Lemmas 7.1 and 7.5, and Corollary 7.6, pp. 65 and 99–102. [https://arxiv.org/abs/1912.13213v10](https://arxiv.org/abs/1912.13213v10).

<a id="ref-farina"></a>

**\[2\]** Gabriele Farina, Christian Kroer, and Tuomas Sandholm. *Faster Game Solving via Predictive Blackwell Approachability: Connecting Regret Matching and Mirror Descent*. AAAI, 2021. Full version: arXiv:2007.14358v2. [https://arxiv.org/abs/2007.14358v2](https://arxiv.org/abs/2007.14358v2). Algorithms 4–5 and Theorems 1–2.

<a id="ref-houlimit"></a>

**\[3\]** Oskari Tammelin, Neil Burch, Michael Johanson, and Michael Bowling. *Solving Heads-Up Limit Texas Hold'em*. IJCAI, 2015. [PDF](https://ijcai.org/proceedings/2015/0097.pdf).

<a id="ref-flaspohler"></a>

**\[4\]** Genevieve Flaspohler, Francesco Orabona, Judah Cohen, Soukayna Mouatadid, Miruna Oprescu, Paulo Orenstein, and Lester Mackey. *Online Learning with Optimism and Delay*. ICML, 2021. [PDF](https://proceedings.mlr.press/v139/flaspohler21a/flaspohler21a.pdf).

{% endraw %}
