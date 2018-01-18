---
layout: post
title: Member lower bounds?
---

**Theorem**: Consider a strategy that satisfies $E[T_i(n)] = o(n^a)$ for any set of Bernoulli reward distributions, any arm $i$ with $\Delta_i > 0$, and any $a > 0$. Then, for any set of Bernoulli reward distributions the following holds:

$$ \lim_{n\to +\infty} \inf\frac{\bar{R}_n}{\ln n}\geq \sum_{i: \Delta_i > 0} \frac{\Delta_i}{kl(\mu_i, \mu^*)}  $$

Using inequalities:

$$ 2(p-q)^2 \leq kl(p,q) \leq\frac{(p-q)^2}{q(1-q)} $$

*Proof*. bla bla bla

**1. Notation**

Wlog, assume $\mu_2 < \mu_1 < 1$. Let $\varepsilon > 0$. We can find bla bla such that:

$$ kl(\mu_2, \mu_2') \leq (1 + \varepsilon)kl(\mu_2, \mu_1) $$
