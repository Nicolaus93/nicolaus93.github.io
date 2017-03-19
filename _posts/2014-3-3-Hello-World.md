---
layout: post
title: Unbiased estimators
---

Variance? Whaaaat?

We have $X_1, X_2, ... $ independent and identically distributed random variables with $E[X_i]=\mu$ and $Var[X_i]=\sigma^2 < + \infty$. We set $ \bar{X}\_n=\frac{1}{n}\sum\_{i=1}^n X_i $.

A well known estimator of variance in statistics is:

$$ S_n^2 = \frac{1}{n-1} \sum_{i=1}^n (X_i - \bar{X}_n)^2 \tag{1}\label{1}$$

Is it unbiased? How? We have to show that $ S_n^2 \overset{P}{\to} \sigma^2 $ as $ n \to +\infty$.

First of all, we can rewrite the expression for $S_n$. We observe the following:

$$ \sum_{i=1}^n (X_i -\bar{X}_n)^2 = \sum_{i=1}^n (X_i -\mu)^2 - n(\bar{X}_n-\mu)^2 $$

Indeed:

\begin{align}
  \sum\_{i=1}^n (X_i -\bar{X}\_n)^2 & = \sum\_{i=1}^n ((X_i -\mu) - (\bar{X}\_n - \mu))^2 \\
   & = \sum\_{i=1}^n ((X_i -\mu)^2 + \sum\_{i=1}^n ((\bar{X}\_n -\mu)^2 - 2(\bar{X}\_n - \mu)\sum\_{i=1}^n(X_i - \mu) \tag{2}\label{2}
   & = \sqrt{\frac{73^2}{12^2}}\sqrt{\frac{73^2-1}{73^2}} \\
   & = \frac{73}{12}\sqrt{1 - \frac{1}{73^2}} \\ 
\end{align}

And we have that:

$$ \sum_{i=1}^n (\bar{X}_n -\mu)^2 = n (\bar{X}_n -\mu)^2  $$

and:

$$ \sum_{i=1}^n(X_i - \mu) = \sum_{i=1}^n X_i - n\mu = n\bar{X}_n - n\mu = n(\bar{X}_n - \mu) $$ 

Thus in $\eqref{2}$:

$$ \sum_{i=1}^n (X_i -\mu)^2 + \sum_{i=1}^n (\bar{X}_n -\mu)^2 - 2(\bar{X}_n - \mu)\sum_{i=1}^n(X_i - \mu) = $$

$$ \sum_{i=1}^n (X_i -\mu)^2 + n (\bar{X}_n -\mu)^2 - 2n (\bar{X}_n -\mu)^2  $$ 

$$ \sum_{i=1}^n (X_i -\mu)^2 - n (\bar{X}_n -\mu)^2 $$

as claimed.

