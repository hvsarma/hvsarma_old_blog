---
layout: post
title: Lessons learnt - how to build Linear Regression Models
---

Characteristics of a regression model is that - there is a probability distribution of Y for each of X, and the means of these probability distributions vary with X.


####Have we done these steps while doing linear regression? (or any regression for that matter?)

1. Fit a regression function.

2. Plot the function (line or curve) and data. Does it fit the data?

3. Interpret b0, b1, b2, .... Are they meaningful? Do they lie with a reasonable confidence interval range? What does it mean if the interval contains a zero?

4. Obtain a point estimate of the response variable (y) when X=0. Is the estimate meaningful or reliable?

5. How about a t-test to determine a linear relation?

6. What is the model p-value? Interpret.

7. Determine the power of the test for b1, b2, etc.

8. Interpret R-squared (vs. Adj.R-squared), and r (correlation).

9. What do qq-plots, residual plots tell?

10. How do we do an F-test to determine the fit of a linear function?

11. When do we transform X's, or Y? Which lambda value to choose in box-cox transformation, and why?

12. How do we measure reliability of the model? How does cross validation differ from resampling?

An important point to note is that linearity refers to coeficients, and not the X's, hence a polynomial function of Xs is a linear regression function.
