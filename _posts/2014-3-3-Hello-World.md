---
layout: post
title: Unbiased estimators
---

Variance? What is that?

We have $X_1, X_2, ... $ independet and identically distributed random variables with $E[X_i]=\mu$ and $Var[X_i]=\sigma^2 < + \infty$. We set $\bar{X}_n = \frac{1}{n}\sum_{i=1}^n X_i$.
A well known estimator of variance in statistics is:

$$ S_n^2 = \frac{1}{n-1} \sum_{i=1}^n (X_i -\mu)^2 - n(\bar{X}_n-\mu)^2 $$

Is it unbiased? How? We have to show that $ S_n^2 \overset{P}{\to} \sigma^2 $ as $ n \to +\infty$.

$$ a \overset{p}{\to} b $$
$$a^2 + b^2 = c^2$$
