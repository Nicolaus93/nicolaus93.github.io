---
layout: post
title: Member gradient descent?
---

The general paradigm of online learning is the following:

$$ a = b $$ 

In general, we can consider a generic linear prediction problem (classification or regression) with loss function $f$. Let's define the loss of $\textbf{w}$ on the example $(\textbf{x}_t, y_t)$ as $l_t(w) = l(\textbf{w}^\top \textbf{x}_t, y_t)$. For example, $l_t(\textbf{w}) = \mathbb{I}\{y_t, \textbf{w}^\top_t \textbf{x} \leq 0\}$ in classification problems, with $y_t \in \{-1, +1\}$, or $l_t(\textbf{w}_t) = (\textbf{w}^\top \textbf{x}_t - y_t)^2$ in regression problems, with $y_t \in \mathbb{R}$. In this case more general case, we can evaluate the algorithm by using the sequential risk:

$$ \frac{1}{T} \sum_{t=1}^T l_t(\textbf{w}_t} $$
