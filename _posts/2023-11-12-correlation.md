---
layout: post
title: Correlation coefficient with matrix multiplication
---

Assume that we have a matrix $M$ where each row represents some observations from a random variable. How do we calculate the correlation matrix, i.e., a matrix where entry $i,j$ gives the correlation between row $i$ and row $j$? 
Note that this matrix is symmetric, since the correlation between row $i$ and row $j$ is the same if we invert the indices.

The formula for the [Pearson correlation coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) between two variables X and Y is given by:

$$ corr(X,Y)=\frac{cov(X,Y)}{\sigma_X \sigma_Y}​ $$

where $\sigma_X$ is the variance of $X$ and the covariance between 2 random variables $X$ and $Y$ is defined as:

$$ cov(X,Y)= \mathrm{E}[(X - \mathrm{E}[X])(Y - \mathrm{E}[Y])] = \frac{1}{n−1} \sum_{i=1}^{n} (X_i−\bar{X})(Y_i−\bar{Y}) $$

where $\bar{X}$ and $\bar{Y}$ are the means of $X$ and $Y$ respectively, and $n$ is the number of observations.

Now, we normalize the variables $X$ and $Y$ by subtracting their means and dividing by their standard deviations, to get:

$$ X^′=\frac{X−\bar{X}}{\sigma_X}  $$ 

and same for $Y$.

Let's calculate the covariance of these normalized variables:

$$ cov(X^′,Y^′) =\frac{1}{n−1} \sum_{i=1}^n (X_i' - \bar{X}')(Y_i' - \bar{Y}) = \frac{1}{n-1} \sum_{i=1}^n X_i' Y_i' $$

since $X^′$ and $Y^′$ are normalized (hence $\bar{X}′=\bar{Y}′=0$). Notice that this is a vector multiplication. 

Now, we define $M'$ as the matrix $M$ where all rows have been normalized. If we extend this to all indices $i$ and $j$ in $M'$, we can compute the covariance between rows by having a matrix $C$ defined as $C = \frac{1}{n-1} M' M^{' \top}$, a matrix multiplication!

On the other hand, we can also expand the covariance of the normalized variables to get:

$$ 
\begin{align}
cov(X^′,Y^′) &=\frac{1}{n−1} \sum_{i=1}^n (X_i' - \bar{X}')(Y_i' - \bar{Y}) = \\
& = \frac{1}{n-1} \sum_{i=1}^n \left( \frac{X_i - \bar{X}}{\sigma_X} - \bar{X}' \right) \left( \frac{Y_i - \bar{Y}}{\sigma_Y} - \bar{Y}' \right) \\
& = \frac{1}{(n-1)\sigma_X \sigma_Y} \sum_{i=1}^n (X_i - \bar{X})(Y_i - \bar{Y}) \\
& = \frac{cov(X, Y)}{\sigma_X \sigma_Y} \\
& = corr(X, Y)
\end{align}
$$

Thus, we can effectively calculate the correlation coefficient between all rows $i$ and $j$ by using matrix multiplication on the normalized matrix $M'$.

Here's a very basic and not optimized implementation in C++, where the observation matrix M is stored in [row-major format](https://en.wikipedia.org/wiki/Row-_and_column-major_order):

{% highlight cpp%}

std::vector<double> normalize(int ny, int nx, const float *data) {
  std::vector<double> matrix(ny * nx, 0);

  for (int i = 0; i < ny; ++i) {
    double sum = 0, sum_sq = 0;
    for (int j = 0; j < nx; ++j) {
      double val = data[j + i * nx];
      sum += val;
      sum_sq += val * val;
    }
    double mean = sum / nx;
    double variance = (sum_sq / nx) - (mean * mean);
    double std = sqrt(variance * nx / (nx - 1));

    for (int j = 0; j < nx; ++j) {
      matrix[j + i * nx] = (data[j + i * nx] - mean) / std;
    }
  }

  return matrix;
}


/*
- input rows: 0 <= y < ny
- input columns: 0 <= x < nx
- element at row y and column x is stored in data[x + y*nx]
- correlation between rows i and row j has to be stored in result[i + j*ny]
*/
void correlate(int ny, int nx, const float *data, float *result) {

  auto mat = normalize(ny, nx, data);
  for (int i = 0; i < ny; ++i) {
    for (int j = i; j < ny; ++j) {
      if (i == j) {
        result[i + j * ny] = 1;
        continue;
      }
      // row_i * row_j
      double res = 0;
      for (int k = 0; k < k < nx; ++k) {
        res += mat[k + i * nx] * mat[k + j * nx];
      }
      result[i + j * ny] = res / (nx - 1);
      result[j + i * ny] = res / (nx - 1);
    }
  }
}
{% endhighlight %}

