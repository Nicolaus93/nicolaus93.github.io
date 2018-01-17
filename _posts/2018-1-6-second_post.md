---
layout: post
title: Member lower bounds?
---

**Theorem**: Consider a strategy that satisfies $E[T_i(n)] = o(n^a)$ for any set of Bernoulli reward distributions, any arm $i$ with $\Delta_i > 0$, and any $a > 0$. Then, for any set of Bernoulli reward distributions the following holds:

$$ \lim_{n\to +\infty} \inf\frac{\bar{R}_n}{\ln n}\geq \sum_{i: \Delta_i > 0} \frac{\Delta_i}{kl(\mu_i, \mu^*)}  $$

*Proof*. bla bla bla
