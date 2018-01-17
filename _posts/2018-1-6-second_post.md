---
layout: post
title: Stochastic bandits lower bound.
---

Consider a strategy that satisfies $E[T_i(n)] = o(na)$ for any set of Bernoulli reward distributions, any arm $i$ with $\Delta_i > 0$, and any $a > 0$. Then, for any set of Bernoulli reward distributions the following holds:

$$ \lim_{n\to +\infty} \inf\frac{\bar{R}_n}{\ln n}\geq \sum_{i: \Delta_i > 0} \frac{\Delta_i}{kl(\mu_i, \mu^*})  $$

Assuming we have $X_1, X_2, ..., X_n $ independent and identically distributed random variables with $\mathrm{E}[X_i]=\mu$ and $\mathrm{Var}[X_i]=\sigma^2 < + \infty$, we set $ \bar{X}\_n=\frac{1}{n}\sum\_{i=1}^n X_i $. Then a well known estimator of variance in statistics is:

$$ S_n^2 = \frac{1}{n-1} \sum_{i=1}^n (X_i - \bar{X}_n)^2 \tag{1}\label{1}$$

