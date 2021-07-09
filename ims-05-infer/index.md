# Statistical inference

## Tutorial description

Categorial data is all around us. It’s in the latest opinion polling numbers, in the data that lead to new breakthroughs in genomics, and in the troves of data that internet companies collect to sell products to you. In this tutorial you’ll learn techniques for parsing the signal from the noise; tools for identifying when structure in this data represents interesting phenomena and when it is just random noise.

This tutorial also builds on statistical inference ideas we’ve learned about so far and applies them to situations where the response variable for our analysis is a numerical variable. In this tutorial we will learn how to conduct statistical inference in such scenarios using both simulation based methods as well as methods based on the central limit theorem.
We will encounter several new point estimates and a couple new distributions. In each case, the inference ideas remain the same: determine which point estimate or test statistic is useful, identify an appropriate distribution for the point estimate or test statistic, and apply the ideas of inference.

## Learning objectives

- Construct a confidence interval on one proportion and the difference in two proportions.
- Interpret a confidence interval in the context of a particular data set.
- Conduct hypothesis tests for a single proportion and the difference in two proportions.
- Carry out a Chi-squared Goodness of Fit Test and a Chi-squared Test of Independence.
- Visualize the null distribution of a statistic and use it to calculate a p-value.
- Interpret a p-value in the context of a particular problem.
- Explain the distinction type of statistical errors analysts are susceptible to when carrying out a hypothesis test.
- Construct a confidence interval for a median using bootstrapping.
- Construct a confidence interval for a mean using both bootstrapping and the t-distribution.
- Construct a confidence interval for the difference between two means using both bootstrapping and the t-distribution.
- Conduct inference on means when samples are dependent.
- Interpret all results within the context of your data and your research question.

## Lessons

### 1 - [Inference for a single proportion](https://bghammill.github.io/ims-05-infer/ims-05-lesson-01/)

In this lesson you will learn how to form a confidence interval on a single proportion using bootstrapping and an approximation-based method. You’ll explore how the sample size and the true proportion effect the width of the interval.

### 2 - [Hypothesis tests to compare proportions](https://bghammill.github.io/ims-05-infer/ims-05-lesson-02/)

This lesson dives deeper into inference for a single parameter by performing hypothesis tests. Then, you’ll extend this technique to the difference between two proportions. Finally, this lesson wraps up with an exploration of what happens when you know the null hypothesis is true and the types of statistical errors that are baked in to this approach.

### 3 - [Chi-squared test of independence](https://bghammill.github.io/ims-05-infer/ims-05-lesson-03/)

This part of the tutorial will teach you how to use both bootstrapping and approximation methods to test for the independence of two categorical variables. This commonly-used method is called the chi-squared Test of Independence.

### 4 - [Chi-squared goodness of fit test](https://bghammill.github.io/ims-05-infer/ims-05-lesson-04/)

The tutorial wraps up with two case studies using election data. Here, you’ll learn how to a second Chi-squared test to check the goodness of fit between your data and a hypothetical distribution. You’ll study election results from Iran and Iowa and evaluate whether we can detect fraud using something called Benford’s Law.

### 5 - [Bootstrapping for estimating a parameter](https://bghammill.github.io/ims-05-infer/ims-05-lesson-05/)

Construct confidence intervals for a median, mean, and any other statistic for the distribution of a numerical variable using bootstrapping.

### 6 - [Introducing the t-distribution](https://bghammill.github.io/ims-05-infer/ims-05-lesson-06/)

Introduce the t-distribution for theoretical inference on means and constrast it to the normal distribution.

### 7 - [Inference for difference in two means](https://bghammill.github.io/ims-05-infer/ims-05-lesson-07/)

Use simulation based and theoretical methods for inference in the difference of two means.

### 8 - [Comparing many means](https://bghammill.github.io/ims-05-infer/ims-05-lesson-08/)

Compare many means using ANOVA.

