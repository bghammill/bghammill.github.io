# Foundations of inference

## Tutorial description

One of the fundamental tasks in science and other disciplines is using a sample of data to understand aspects of a larger population. The conclusions made about that population use a method referred to as **statistical inference**. Although seemingly backwards, the logic behind statistical inference is to reject a research claim which is not of interest (thereby validating the claim of interest). For example, in order to show that one diet is better than the other, you first reject the idea that the two diets are equally effective at causing weight loss.

Statistical inference plays a role in many different data analyses (including the linear models you’ve seen in previous tutorials). In this tutorial, we introduce the idea of a p-value which can be thought of as the degree of disagreement between the data and the research claim. We also introduce confidence intervals which provide a range of plausible values for the measure of interest (e.g., the number of additional pounds lost, on average, for the first diet as compared to the second diet).

## Learning objectives

- Translate research hypotheses into statistical hypotheses
- Perform a randomization test for two proportions 
- Interpret the results from the randomization test in the context of the research
- Describe different (and their impact on the research conclusions) errors that might have been present
- Create a bootstrap confidence interval for a single proportion 

## Lessons

### [Sampling variability](https://bghammill.github.io/ims-04-foundations/ims-04-lesson-01/)

The fundamental principle that there is variability across different samples will be investigated in this lesson. You will be able to characterize the variability across samples as compared to the underlying population. Remember, the goal of the research is to understand the population, while the information you have comes only from a single sample of data.

### [Randomization test & p-values](https://bghammill.github.io/ims-04-foundations/ims-04-lesson-02/)

Using a custom SAS macro, you will be able to work through an entire randomization test. For a given dataset and research question, you will identify hypotheses and decide whether or not it is appropriate to reject the null hypothesis in favor of the research claim of interest.

### [Randomization test & critical regions](https://bghammill.github.io/ims-04-foundations/ims-04-lesson-05/)

As an alternative to p-values, we can also calculate critical regions of the null distribution to use for making decisions about statistical significance.

### [Errors in hypothesis testing](https://bghammill.github.io/ims-04-foundations/ims-04-lesson-03/)

Working through another complete hypothesis test, you will focus on how and when false conclusions are sometimes drawn. You will learn to distinguish between a Type I error where you are confident in a claim that isn’t valid and a Type II error where you fail to conclude anything when your science is correct. Additionally, you will learn the important role that number of observations play in reducing error rates.

### Parameters and confidence intervals

Additionally, you will learn to create confidence intervals for a single population proportion. A confidence interval will provide a range of plausible values to estimate the unknown population value. The parameter is the true unknown proportion of successes in the population, estimated using the sample proportion. Bootstrapping allows for estimation of the variability of the sample proportion which leads to an interval estimate.



# [< Back to main page](https://bghammill.github.io/)

