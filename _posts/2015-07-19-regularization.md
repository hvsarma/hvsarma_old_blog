---
layout: post
title: Lessons learnt - Bias or variance problem in regression? Regularization could be the solution to it! 
---

Before I discuss about the regularization in the context of (linear) regression, it is important to understand under-fitting and over-fitting. 

- When a polynomial function or large set of data are used on the model, over-fitting is likely. 

- When a linear function or fewer features set are used, then model will likely under-fit on the data, unless it is a deterministic relationship. 

- In both cases, model will not be able to generalize for new data and prediction error will be large, so we require "right-fit" on the data.

Solution:
Regularization will penalize the theta parameters in the cost function to prevent overfitting. (i.e. number of predictors > number of observations).

Two types of regularizations most commonly understood and used are:

1. Ridge, aka L2 norm (similar to euclidean distance)

2. Lasso, aka L1 norm (similar to manhattan distance)

Another popular regularization technique is the Elastic Net, the convex combination of the L2 norm and the L1 norm.

L2 and L1 regularization differ in how they cope with correlated predictors: L2 will divide the coefficient loading equally among them whereas L1 will place all the loading on one of them while shrinking the others towards zero. Elastic Net combines the advantages of both: it tends to either select a group of correlated predictors in which case it puts equal loading on all of them, or it completely shrinks the group.

Scikit-learn provides separate classes for LASSO and Elastic Net: sklearn.linear_model.Lasso and sklearn.linear_model.ElasticNet. In contrast to RidgeRegression, the solution for both LASSO and Elastic Net has to be computed numerically. The classes above use an optimization technique called coordinate descent. Alterntively, you can also use the class sklearn.linear_model.SGDRegressor which uses stochastic gradient descent instead and often is more efficient for large-scale, high-dimensional and sparse data.

The following code snippet is an example taken from sklearn documentation. 

Note: Though lambda is the statistical term used for smoothing, sklearn uses **alpha** for regularization paramter.

{% highlight ruby %}
from sklearn.linear_model import Lasso

fig, ax_rows = plt.subplots(2, 2, figsize=(8, 5))

degree = 9
alphas = [1e-3, 1e-2]
for alpha, ax_row in zip(alphas, ax_rows):
    ax_left, ax_right = ax_row
    est = make_pipeline(PolynomialFeatures(degree), Lasso(alpha=alpha))
    est.fit(X_train, y_train)
    plot_approximation(est, ax_left, label='alpha=%r' % alpha)
    plot_coefficients(est, ax_right, label='Lasso(alpha=%r) coefficients' % alpha, yscale=None)

plt.tight_layout()
{% endhighlight %}



