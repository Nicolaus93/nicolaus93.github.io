---
layout: post
title: Unbiased estimators
---

Variance? Whaaaat?

We have $X_1, X_2, ... $ independent and identically distributed random variables with $E[X_i]=\mu$ and $\mathrm{Var}[X_i]=\sigma^2 < + \infty$. We set $ \bar{X}\_n=\frac{1}{n}\sum\_{i=1}^n X_i $.

A well known estimator of variance in statistics is:

$$ S_n^2 = \frac{1}{n-1} \sum_{i=1}^n (X_i - \bar{X}_n)^2 \tag{1}\label{1}$$

Is it unbiased? How? We have to show that $ S_n^2 \overset{P}{\to} \sigma^2 $ as $ n \to +\infty$.

First of all, we can rewrite the expression for $S_n$. We observe the following:

$$ \sum_{i=1}^n (X_i -\bar{X}_n)^2 = \sum_{i=1}^n (X_i -\mu)^2 - n(\bar{X}_n-\mu)^2 $$

Indeed:

$$
\begin{align}
  \sum_{i=1}^n (X_i -\bar{X}_n)^2 & = \sum_{i=1}^n ((X_i -\mu) - (\bar{X}_n - \mu))^2 \\
   & = \sum_{i=1}^n (X_i -\mu)^2 + \sum_{i=1}^n (\bar{X}_n -\mu)^2 - 2(\bar{X}_n - \mu)\sum_{i=1}^n(X_i - \mu) \tag{2}\label{2}
\end{align}
$$

And we have that:

$$ \sum_{i=1}^n (\bar{X}_n -\mu)^2 = n (\bar{X}_n -\mu)^2  $$

and:

$$ \sum_{i=1}^n(X_i - \mu) = \sum_{i=1}^n X_i - n\mu = n\bar{X}_n - n\mu = n(\bar{X}_n - \mu) $$ 

Thus in $\eqref{2}$:

$$ \sum_{i=1}^n (X_i -\mu)^2 + \sum_{i=1}^n (\bar{X}_n -\mu)^2 - 2(\bar{X}_n - \mu)\sum_{i=1}^n(X_i - \mu) = $$

$$ \sum_{i=1}^n (X_i -\mu)^2 + n (\bar{X}_n -\mu)^2 - 2n (\bar{X}_n -\mu)^2  $$ 

$$ \sum_{i=1}^n (X_i -\mu)^2 - n (\bar{X}_n -\mu)^2 $$

as claimed.

Then in $\eqref{1}$:

$$
\begin{align}
 S^2_n & = \frac{1}{n-1} \sum_{i=1}^n (X_i - \bar{X}_n)^2 \\
   & = \frac{1}{n-1} \sum_{i=1}^n (X_i -\mu)^2 + \frac{n}{n-1} \sum_{i=1}^n (\bar{X}_n -\mu)^2 \tag{3}\label{3}
\end{align}
$$

We now consider the first term in $\eqref{3}$:

$$ \frac{1}{n-1} \sum_{i=1}^n (X_i -\mu)^2 = \frac{n}{n-1}\frac{1}{n} \sum_{i=1}^n (X_i -\mu)^2 $$ 

We can use the (weak) law of large numbers to get:

$$ \frac{1}{n} \sum_{i=1}^n (X_i - \mu)^2  \overset{P}{\to} E[X-\mu]^2 = \mathrm{Var}[X] = \sigma^2$$
