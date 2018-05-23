---
layout: post
title: Member Sherman-Morrison?
---

{% highlight python %}
def sherman_morrison(M_inv, x):
    """
    Input:
        - x: (np.array) column vector
        - M_inv: (np.array) inverse of M matrix
    Output:
        (M + x*x')^-1 computed using Sherman-Morrison formula
    """
    x = x.reshape((-1, 1))
    M_inv -= M_inv.dot(x.dot(x.T.dot(M_inv))) / (1 + x.T.dot(M_inv.dot(x)))
    return M_inv
{% endhighlight %}
