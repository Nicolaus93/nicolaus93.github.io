---
layout: post
title: "Regret Matching, with and without the Plus"
---

{% raw %}

Speaking of algorithms, I started looking into regret matching some time ago as a nice example of an algorithm that needs no learning-rate tuning. It is tempting to call such an algorithm *parameter-free*, but that term has a more specific meaning in optimization and online learning.

My friend Francesco makes this distinction explicit in Remark 1 of *Normalized Gradients for All* [4, Remark 1, p. 7](#ref-orabona2023). In the stochastic convex optimization setting described there, parameter-free algorithms achieve a convergence rate of the form

$$
\widetilde O\!\left(\frac{\lVert\boldsymbol{x}_1-\boldsymbol{x}^{\star}\rVert}{\sqrt T}\right)
$$

uniformly over convex objectives with bounded stochastic subgradients, without knowing the distance from the initial point $$\boldsymbol{x}_1$$ to a minimizer $$\boldsymbol{x}^{\star}$$. Here $$\lVert\cdot\rVert$$ denotes an arbitrary norm, not necessarily the Euclidean norm; stochastic subgradients are bounded in the corresponding dual norm $$\lVert\cdot\rVert_\ast$$, with that bound normalized to one. The quantity $$T$$ is the number of stochastic subgradient queries, and $$\widetilde O$$ suppresses polylogarithmic factors. The guarantee may be in expectation or with high probability, and knowledge of a subgradient bound may be allowed. An analogous definition uses comparator-dependent regret in online convex optimization.

The important point is that this is a guarantee about adaptation, not a literal inventory of the algorithm's inputs. An algorithm can be parameter-free in this technical sense and still have quantities to tune, or require information about the objective. As the remark explains, the name comes from the fact that many algorithms achieving this guarantee happen to have no learning rate; that is not true of all of them.

Coming back to regret matching, the absence of a learning rate and a regret bound that holds simultaneously for all competitors do not, by themselves, establish this stronger kind of optimal adaptation. In fact, RM does not achieve the optimal dependence on the number of experts: its worst-case regret scales as $$\sqrt{dT}$$ rather than $$\sqrt{T\log d}$$, where $$d$$ is the number of experts. Both RM and RM+ nevertheless have a useful invariance: they are scale-free. Section [5](#sec-scalefree) makes this distinction precise. In the remaining of the post, I assume that losses are bounded in $$[0,1]$$. However, if losses lie in an interval of width $$L$$, the standard bound becomes $$L\sqrt{dT}$$, but the algorithm doesn't need to know $$L$$!

This post develops the updates and regret guarantees directly, with particular attention to what clipping changes.

## 1. Setup: learning with expert advice

There are $$n$$ experts. At round $$t$$, before seeing their current losses, an algorithm $$\mathcal{A}$$ chooses a distribution

$$
\boldsymbol{x}_t\in\Delta_n
:=\left\{\boldsymbol{x}\in\mathbb{R}^n_{\ge0}:\sum_{i=1}^n x_i=1\right\}.
$$

Then $$\mathcal{A}$$ observes the entire loss vector $$\boldsymbol{\ell}_t\in[0,1]^n$$ and incurs $$\langle \boldsymbol{x}_t,\boldsymbol{\ell}_t\rangle$$. This is the full-information setting: $$\mathcal{A}$$ observes the loss of every expert, including those to which it assigned no probability. The distribution can describe a convex combination of predictions, or the expected loss of randomly choosing an expert. Vector quantities are written in bold; their scalar coordinates use ordinary type.

For a fixed competitor $$\boldsymbol{q}\in\Delta_n$$, define

<a id="eq-regret"></a>

$$
\operatorname{Reg}_T(\boldsymbol{q})=\sum_{t=1}^T\langle \boldsymbol{x}_t-\boldsymbol{q},\boldsymbol{\ell}_t\rangle.
\tag{1}
$$

The competitor is fixed across rounds, but we can choose it in hindsight. In particular, $$\boldsymbol{q}=\boldsymbol{e}_i$$ means always following expert $$i$$. Since the competitor's cumulative loss is linear in $$\boldsymbol{q}$$, the best distribution in hindsight is attained at a best expert. Allowing all distributions still matters when we want a guarantee that reflects the particular competitor.

We do not assume that the loss vectors are independent or sampled from a fixed distribution. They may depend on past play. A no-regret guarantee asks for $$\max_{\boldsymbol{q}\in\Delta_n}\operatorname{Reg}_T(\boldsymbol{q})=o(T)$$ uniformly over allowed loss sequences: the average loss gap goes to zero.

Now define the instantaneous regret vector

<a id="eq-instantaneous"></a>

$$
\boldsymbol{r}_t=\langle \boldsymbol{x}_t,\boldsymbol{\ell}_t\rangle\mathbf 1-\boldsymbol{\ell}_t,
\qquad \boldsymbol{R}_t=\sum_{s=1}^t \boldsymbol{r}_s,\qquad \boldsymbol{R}_0=\mathbf{0}.
\tag{2}
$$

Coordinate $$r_{t,i}$$ is how much better expert $$i$$ did than $$\mathcal{A}$$ did on that round. A positive coordinate is an opportunity the algorithm missed. Two identities will do most of the work:

<a id="eq-identities"></a>

$$
\operatorname{Reg}_T(\boldsymbol{q})=\langle \boldsymbol{q},\boldsymbol{R}_T\rangle,\qquad \langle \boldsymbol{x}_t,\boldsymbol{r}_t\rangle=0.
\tag{3}
$$

The second follows immediately from $$\langle \boldsymbol{x}_t,\mathbf1\rangle=1$$. Regret matching chooses its next distribution using the positive coordinates of this cumulative opportunity vector.

## 2. Regret matching: a proof from the book

For a vector $$\boldsymbol{v}$$, let $$\left[\boldsymbol{v}\right]_+$$ denote its coordinatewise positive part. Write $$\mathcal N(\boldsymbol{v})=\boldsymbol{v}/\lVert \boldsymbol{v}\rVert_1$$ for a nonzero nonnegative vector $$\boldsymbol{v}$$. When $$\boldsymbol{v}=\mathbf{0}$$, fix a fallback distribution, say the uniform distribution. Regret matching was first introduced by Hart and Mas-Colell [1](#ref-hart2000). Here we use its external-regret version, which competes with fixed experts. Ordinary regret matching (RM) is

<a id="eq-rm"></a>

$$
\boxed{\quad \boldsymbol{x}_t=\mathcal N\bigl(\left[\boldsymbol{R}_{t-1}\right]_+\bigr),
\qquad \boldsymbol{R}_t=\boldsymbol{R}_{t-1}+\boldsymbol{r}_t.\quad}
\tag{4}
$$

An expert gets probability proportional to how much better it would have done so far. Experts with nonpositive cumulative regret get no probability, unless the whole positive part vanishes. Crucially, negative coordinates are kept in the state $$\boldsymbol{R}_t$$. An expert that performed badly in the past must work off that deficit before receiving positive probability again.

### The scalar positive-part inequality

Surprisingly, ChatGPT Sol 6.1 found a "proof from the book" of its regret using the following inequality valid for all scalars $$a,b\in\mathbb{R}$$,

<a id="eq-scalarpositivepart"></a>

$$
[a+b]_+^2\le [a]_+^2+2[a]_+b+b^2.
\tag{5}
$$

For a scalar $$u\in\mathbb{R}$$, write $$[u]_+=\max\{u,0\}$$. We prove [(5)](#eq-scalarpositivepart) for all real $$a,b$$ by separating the two possible signs of $$a$$.

If $$a\ge0$$, then $$[a]_+=a$$ and $$[a+b]_+^2\le(a+b)^2$$, so

$$
[a+b]_+^2\le(a+b)^2
=a^2+2ab+b^2=[a]_+^2+2[a]_+b+b^2.
$$

The first inequality holds because taking the positive part either leaves $$a+b$$ unchanged or replaces it by zero.

If $$a<0$$, then $$[a]_+=0$$. Since $$a+b\le b$$ and the positive-part function is nondecreasing,

$$
0\le[a+b]_+\le[b]_+\le\lvert b\rvert.
$$

Squaring gives

$$
[a+b]_+^2\le b^2=[a]_+^2+2[a]_+b+b^2.
$$

Together the two cases prove the claim. In particular, the proof does not require either $$b$$ or $$a+b$$ to be nonnegative.

### The regret bound

Applying [(5)](#eq-scalarpositivepart) coordinatewise with $$a=R_{t-1,i}$$ and $$b=r_{t,i}$$ gives

$$
\lVert \left[\boldsymbol{R}_t\right]_+\rVert_2^2
\le \lVert \left[\boldsymbol{R}_{t-1}\right]_+\rVert_2^2
+2\langle \left[\boldsymbol{R}_{t-1}\right]_+,\boldsymbol{r}_t\rangle+\lVert \boldsymbol{r}_t\rVert_2^2
= \lVert \left[\boldsymbol{R}_{t-1}\right]_+\rVert_2^2+\lVert \boldsymbol{r}_t\rVert_2^2
$$

To see why $$\langle \left[\boldsymbol{R}_{t-1}\right]_+,\boldsymbol{r}_t\rangle = 0$$, suppose $$\left[\boldsymbol{R}_{t-1}\right]_+\ne\mathbf{0}$$ and write $$Z_{t-1}=\lVert \left[\boldsymbol{R}_{t-1}\right]_+\rVert_1>0$$. The normalization in [(4)](#eq-rm) says exactly that

$$
\boldsymbol{x}_t=\frac{\left[\boldsymbol{R}_{t-1}\right]_+}{Z_{t-1}},
\qquad\text{hence}\qquad
\left[\boldsymbol{R}_{t-1}\right]_+=Z_{t-1}\boldsymbol{x}_t.
$$

Thus the positive regret vector is a positive scalar multiple of $$\boldsymbol{x}_t$$, and [(3)](#eq-identities) gives

$$
\langle \left[\boldsymbol{R}_{t-1}\right]_+,\boldsymbol{r}_t\rangle
=Z_{t-1}\langle \boldsymbol{x}_t,\boldsymbol{r}_t\rangle=0.
$$

If $$\left[\boldsymbol{R}_{t-1}\right]_+=\mathbf{0}$$, the inner product is zero directly, regardless of the fallback distribution. Telescoping from $$\boldsymbol{R}_0=\mathbf{0}$$ yields

<a id="eq-rmguarantee"></a>

$$
\operatorname{Reg}_T(\boldsymbol{q})\le\langle \boldsymbol{q},\left[\boldsymbol{R}_T\right]_+\rangle
\le\lVert \boldsymbol{q}\rVert_2\lVert \left[\boldsymbol{R}_T\right]_+\rVert_2
\le\lVert \boldsymbol{q}\rVert_2\sqrt{\sum_{t=1}^T\lVert \boldsymbol{r}_t\rVert_2^2}.
\tag{6}
$$

Here $$\boldsymbol{q}\ge\mathbf{0}$$ justifies the first inequality. The same run satisfies this bound for every $$\boldsymbol{q}$$; it never needs to know the competitor in advance. For losses in $$[0,1]^n$$, each $$\lvert r_{t,i}\rvert\le1$$, giving the familiar $$\operatorname{Reg}_T(\boldsymbol{q})\le\lVert \boldsymbol{q}\rVert_2\sqrt{nT}$$.

When asking ChatGPT about the origin of this argument, it couldn't really trace it to any of the existing papers. However, I would be glad to give proper credits to the original authors if such argument is already known.

## 3. Regret matching+: clip the state after every update

Regret matching+ (RM+), developed by Tammelin and presented with Burch, Johanson, and Bowling [2](#ref-tammelin2015), changes where the positive part is taken. It keeps a nonnegative state $$\boldsymbol{Q}_t$$, initialized at $$\boldsymbol{Q}_0=\mathbf{0}$$, and uses

<a id="eq-rmplus"></a>

$$
\boxed{\quad \boldsymbol{x}_t=\mathcal N(\boldsymbol{Q}_{t-1}),
\qquad \boldsymbol{Q}_t=\left[\boldsymbol{Q}_{t-1}+\boldsymbol{r}_t\right]_+.\quad}
\tag{7}
$$

The regret increment still uses the loss of the distribution actually played on that round.

The difference is easy to miss. RM takes the positive part of a sum; RM+ repeatedly clips partial sums. In general,

$$
\left[\left[a\right]_++b\right]_+\ne\left[a+b\right]_+.
$$

For example, a coordinate receiving increments $$-2$$ and then $$+1$$ ends at $$-1$$ in RM's cumulative state, so its positive part is zero. RM+ clips it to zero after the first increment and reaches $$1$$ after the second. These numbers illustrate the state recurrence; they are not a complete loss sequence, since the increments themselves depend on play.

RM+ therefore forgets negative balances as they occur. Its state $$\boldsymbol{Q}_t$$ is not the cumulative regret vector $$\boldsymbol{R}_t$$. To relate them, introduce the clipping correction

<a id="eq-correction"></a>

$$
\boldsymbol{c}_t=\left[-\boldsymbol{Q}_{t-1}-\boldsymbol{r}_t\right]_+,\qquad \boldsymbol{C}_t=\sum_{s=1}^t \boldsymbol{c}_s.
\tag{8}
$$

To see what this correction does, use the scalar identity $$[u]_+=u+[-u]_+$$: if $$u\ge0$$, the second term is zero; if $$u<0$$, the second term is $$-u$$ and cancels the negative value. Applying this identity coordinatewise to the RM+ update gives

$$
\boldsymbol{Q}_t=\left[\boldsymbol{Q}_{t-1}+\boldsymbol{r}_t\right]_+
=\boldsymbol{Q}_{t-1}+\boldsymbol{r}_t
+\underbrace{\left[-\boldsymbol{Q}_{t-1}-\boldsymbol{r}_t\right]_+}_{\boldsymbol{c}_t}.
$$

Thus $$\boldsymbol{c}_t$$ adds back exactly the amount needed to bring each negative coordinate of the unprojected update to zero, and leaves nonnegative coordinates unchanged. This rewrites the same update as $$\boldsymbol{Q}_t=\boldsymbol{Q}_{t-1}+\boldsymbol{r}_t+\boldsymbol{c}_t$$. Summing these increments from $$\boldsymbol{Q}_0=\mathbf{0}$$ gives

<a id="eq-state"></a>

$$
\boldsymbol{Q}_t=\boldsymbol{R}_t+\boldsymbol{C}_t,\qquad \boldsymbol{c}_t\ge\mathbf{0},\qquad \langle \boldsymbol{c}_t,\boldsymbol{Q}_t\rangle=0.
\tag{9}
$$

For the last identity, fix a coordinate $$i$$ and write $$u_i=Q_{t-1,i}+r_{t,i}$$. By definition,

$$
Q_{t,i}=[u_i]_+,\qquad c_{t,i}=[-u_i]_+.
$$

If $$u_i\ge0$$, then $$c_{t,i}=0$$; if $$u_i<0$$, then $$Q_{t,i}=0$$. Hence $$c_{t,i}Q_{t,i}=0$$ in every coordinate, and summing gives

$$
\langle \boldsymbol{c}_t,\boldsymbol{Q}_t\rangle=\sum_{i=1}^n c_{t,i}Q_{t,i}=0.
$$

In words, a coordinate is corrected only when its updated state is zero.

The other cancellation we need is $$\langle \boldsymbol{Q}_{t-1},\boldsymbol{r}_t\rangle=0$$. If $$\boldsymbol{Q}_{t-1}\ne\mathbf{0}$$, the RM+ normalization gives $$\boldsymbol{Q}_{t-1}=\lVert \boldsymbol{Q}_{t-1}\rVert_1\boldsymbol{x}_t$$. Therefore

$$
\langle \boldsymbol{Q}_{t-1},\boldsymbol{r}_t\rangle
=\lVert \boldsymbol{Q}_{t-1}\rVert_1\langle \boldsymbol{x}_t,\boldsymbol{r}_t\rangle = 0
$$

from [(3)](#eq-identities). If $$\boldsymbol{Q}_{t-1}=\mathbf{0}$$, the inner product is zero directly, regardless of the fallback distribution.

Recall from [(3)](#eq-identities) that $$\operatorname{Reg}_T(\boldsymbol{q})=\langle \boldsymbol{q},\boldsymbol{R}_T\rangle$$. Since [(9)](#eq-state) gives $$\boldsymbol{R}_T=\boldsymbol{Q}_T-\boldsymbol{C}_T$$, linearity of the inner product and Cauchy–Schwarz give

$$
\begin{aligned}
\operatorname{Reg}_T(\boldsymbol{q})
&=\langle \boldsymbol{q},\boldsymbol{Q}_T\rangle-\langle \boldsymbol{q},\boldsymbol{C}_T\rangle\\
&\le\lVert \boldsymbol{q}\rVert_2\lVert \boldsymbol{Q}_T\rVert_2
-\langle \boldsymbol{q},\boldsymbol{C}_T\rangle.
\end{aligned}
$$

This shows why we need to control $$\lVert \boldsymbol{Q}_T\rVert_2$$: it determines the regret bound, while the nonnegative quantity $$\langle \boldsymbol{q},\boldsymbol{C}_T\rangle$$ can only improve it. We will control the norm by analyzing its square. First, the cancellation $$\langle \boldsymbol{Q}_{t-1},\boldsymbol{r}_t\rangle=0$$ gives

$$
\begin{aligned}
\lVert \boldsymbol{Q}_{t-1}+\boldsymbol{r}_t\rVert_2^2
&=\lVert \boldsymbol{Q}_{t-1}\rVert_2^2
+2\langle \boldsymbol{Q}_{t-1},\boldsymbol{r}_t\rangle+\lVert \boldsymbol{r}_t\rVert_2^2\\
&=\lVert \boldsymbol{Q}_{t-1}\rVert_2^2+\lVert \boldsymbol{r}_t\rVert_2^2.
\end{aligned}
$$

Second, the recurrence says $$\boldsymbol{Q}_{t-1}+\boldsymbol{r}_t=\boldsymbol{Q}_t-\boldsymbol{c}_t$$. Using $$\langle \boldsymbol{Q}_t,\boldsymbol{c}_t\rangle=0$$ therefore gives

$$
\begin{aligned}
\lVert \boldsymbol{Q}_{t-1}+\boldsymbol{r}_t\rVert_2^2
&=\lVert \boldsymbol{Q}_t-\boldsymbol{c}_t\rVert_2^2\\
&=\lVert \boldsymbol{Q}_t\rVert_2^2
-2\langle \boldsymbol{Q}_t,\boldsymbol{c}_t\rangle+\lVert \boldsymbol{c}_t\rVert_2^2\\
&=\lVert \boldsymbol{Q}_t\rVert_2^2+\lVert \boldsymbol{c}_t\rVert_2^2.
\end{aligned}
$$

Equating these two expressions and rearranging yields the exact identity

<a id="eq-energy"></a>

$$
\lVert \boldsymbol{Q}_t\rVert_2^2-\lVert \boldsymbol{Q}_{t-1}\rVert_2^2
=\lVert \boldsymbol{r}_t\rVert_2^2-\lVert \boldsymbol{c}_t\rVert_2^2.
\tag{10}
$$

Telescoping [(10)](#eq-energy) from $$\boldsymbol{Q}_0=\mathbf{0}$$ gives the quantity we needed:

$$
\lVert \boldsymbol{Q}_T\rVert_2^2
=\sum_{t=1}^T\left(\lVert \boldsymbol{r}_t\rVert_2^2-\lVert \boldsymbol{c}_t\rVert_2^2\right)
\le\sum_{t=1}^T\lVert \boldsymbol{r}_t\rVert_2^2.
$$

Taking the square root and substituting into the Cauchy–Schwarz bound above yields

<a id="eq-plusguarantee"></a>

$$
\begin{aligned}
\operatorname{Reg}_T(\boldsymbol{q})&=\langle \boldsymbol{q},\boldsymbol{Q}_T\rangle-\langle \boldsymbol{q},\boldsymbol{C}_T\rangle\\
&\le\lVert \boldsymbol{q}\rVert_2\sqrt{\sum_{t=1}^T
\bigl(\lVert \boldsymbol{r}_t\rVert_2^2-\lVert \boldsymbol{c}_t\rVert_2^2\bigr)}
-\langle \boldsymbol{q},\boldsymbol{C}_T\rangle.
\end{aligned}
\tag{11}
$$

Dropping the clipping improvements recovers the same $$\lVert \boldsymbol{q}\rVert_2\sqrt{nT}$$ bound. Keeping them can say much more on a particular sequence. This does not establish that RM+ dominates RM: the two algorithms generally play different distributions and therefore generate different regret increments.

## 4. A small example where clipping matters in the bound

Suppose there are two experts with constant losses $$\boldsymbol{\ell}_t=(0,1)$$. With the uniform fallback, RM+ starts with

$$
\boldsymbol{x}_1=(1/2,1/2),\qquad \boldsymbol{r}_1=(-\boldsymbol{\ell}_1)+(1/2)\mathbf1=(1/2,-1/2),
$$

so $$\boldsymbol{Q}_1=(1/2,0)$$ and $$\boldsymbol{c}_1=(0,1/2)$$. On every subsequent round,

$$
\boldsymbol{x}_t=(1,0),\qquad \boldsymbol{r}_t=(0,-1),\qquad \boldsymbol{c}_t=(0,1),
\qquad \boldsymbol{Q}_t=(1/2,0).
$$

The regret to the first expert is exactly $$1/2$$, regardless of the horizon. Nevertheless, the coarser bound $$\sqrt{\sum_t\lVert \boldsymbol{r}_t\rVert_2^2}$$ grows as $$\sqrt{T-1/2}$$.

In [(11)](#eq-plusguarantee), each later contribution $$\lVert \boldsymbol{r}_t\rVert_2^2-\lVert \boldsymbol{c}_t\rVert_2^2$$ is zero. With $$\boldsymbol{q}=\boldsymbol{e}_1$$, the correction $$\langle \boldsymbol{q},\boldsymbol{C}_T\rangle$$ is also zero, and the bound gives exactly $$1/2$$. The learner has stopped moving, and the clipping-aware calculation sees that. A worst-case estimate based only on the size of the increments does not. This example explains a benefit of the sharper analysis; ordinary RM also follows the first expert from round two in this particular case.

## 5. Suboptimal regret, but scale-free updates

<a id="sec-scalefree"></a>

Regret matching does not achieve the optimal regret bound for learning with expert advice. Writing $$d=n$$ for the number of experts, the bound proved above is $$\sqrt{dT}$$ against an individual expert. Its polynomial dependence on $$d$$ contrasts with the logarithmic dimension dependence available to exponential weights. For losses in $$[0,1]^d$$, exponential weights with learning rate $$\eta>0$$ satisfies

$$
\max_{1\le i\le d}\operatorname{Reg}_T(\boldsymbol{e}_i)
\le\frac{\log d}{\eta}+\frac{\eta T}{8}.
$$

Optimizing over $$\eta$$ gives $$\sqrt{T\log d/2}$$; the logarithm is inside the square root. Accounting also for the trivial regret bound $$T$$, the minimax benchmark for $$d\ge2$$ is $$\Theta(\min\{T,\sqrt{T\log d}\})$$.

The dimension gap is a limitation of the algorithms, not just of the potential proof. For zero initialization and uniform fallback, both ordinary RM and RM+ have worst-case regret $$\Theta(\min\{T,\sqrt{dT}\})$$. A sharper analysis of these same updates therefore cannot recover the optimal logarithmic dependence on $$d$$.

There is, however, another property worth separating from regret optimality: *RM+ is scale-free*. Orabona points out the scale-free nature of RM in the linked discussion [3](#ref-orabona2020scale); the same invariance holds for RM+. Specifically, multiplying every loss vector by one fixed constant $$\alpha>0$$ leaves all played distributions unchanged. To verify this for RM+, denote quantities on the rescaled loss sequence by primes. Starting from $$\boldsymbol{Q}'_0=\alpha\boldsymbol{Q}_0=\mathbf{0}$$, if $$\boldsymbol{Q}'_{t-1}=\alpha\boldsymbol{Q}_{t-1}$$, normalization gives $$\boldsymbol{x}'_t=\boldsymbol{x}_t$$, including at zero states when the fallback is fixed. Hence $$\boldsymbol{r}'_t=\alpha\boldsymbol{r}_t$$ and

$$
\boldsymbol{Q}'_t
=\left[\boldsymbol{Q}'_{t-1}+\boldsymbol{r}'_t\right]_+
=\left[\alpha(\boldsymbol{Q}_{t-1}+\boldsymbol{r}_t)\right]_+
=\alpha\boldsymbol{Q}_t.
$$

Induction proves the claim. The scaled losses need not stay in $$[0,1]^d$$; the update itself does not require that bound as an input. Ordinary RM has the same invariance, with $$\boldsymbol{R}'_t=\alpha\boldsymbol{R}_t$$. This concerns one common positive rescaling of the loss sequence, not independent rescalings at different rounds.

Thus RM+ needs no learning rate, horizon, or loss-scale input, and its behavior is unchanged by the choice of units for losses!

## References

<a id="ref-hart2000"></a>

**\[1\]** Sergiu Hart and Andreu Mas-Colell. *A Simple Adaptive Procedure Leading to Correlated Equilibrium*. Econometrica, 68(5):1127–1150, 2000. [https://doi.org/10.1111/1468-0262.00153](https://doi.org/10.1111/1468-0262.00153).

<a id="ref-tammelin2015"></a>

**\[2\]** Oskari Tammelin, Neil Burch, Michael Johanson, and Michael Bowling. *Solving Heads-Up Limit Texas Hold'em*. In Proceedings of the Twenty-Fourth International Joint Conference on Artificial Intelligence (IJCAI), pp. 645–652, 2015. [https://ijcai.org/Proceedings/15/Papers/097.pdf](https://ijcai.org/Proceedings/15/Papers/097.pdf).

<a id="ref-orabona2020scale"></a>

**\[3\]** Francesco Orabona (@bremen79). Post on Twitter (now X), February 14, 2020, discussing the scale-free property of regret matching. [https://x.com/bremen79/status/1228318344195035142](https://x.com/bremen79/status/1228318344195035142).

<a id="ref-orabona2023"></a>

**\[4\]** Francesco Orabona. *Normalized Gradients for All*. arXiv:2308.05621v1, 2023. Remark 1, p. 7. [https://arxiv.org/abs/2308.05621v1](https://arxiv.org/abs/2308.05621v1).

{% endraw %}
