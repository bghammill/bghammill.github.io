# Inferential modeling

## Tutorial description

This tutorial combines the statistical inference ideas from the 5th tutorial with the linear regression modeling ideas covered in the 3rd and 4th tutorials. The tutorial starts by understanding how the estimated linear model will change from sample to sample, and you will be able to use that variability to make conclusions about the unknown population linear model. Both interval estimates of the slope and statistical significance will be determined using randomization methods as well as t-based models. Intervals for predicting a single response are shown in contrast to predictions of an average response.

The tutorial will continue using tidy modeling procedures from the **tidyverse** package, including **broom** as a way to work with linear model output.

## Learning objectives

- Describe the variability of sample regression lines (including the impact of variance of observed data and sample size)
- Perform a randomization test to assess the statistical significance of the slope parameter
- Perform a t-test to assess the statistical significance of the slope parameter
- Create a prediction interval for a single response and contrast it with the interval for an average response
- Explain the technical conditions associated with t-test, including why the technical conditions are important for the analysis conclusions
- Experience with when and how to transform variables to meet the technical conditions
- Interpreting coefficients in multiple regression models (including multicollinearity)

## Lessons

### 1 - [Inference in regression](https://bghammill.github.io/ims-06-model-infer/ims-06-lesson-01/)

Recalling how and why we perform inferential analyses on any model, we focus on research questions in which populations are described by a linear model. The variability of the estimated linear model will be compared under different sample sizes (for the data) and different population structures.

### 2 - [Randomization test for slope](https://bghammill.github.io/ims-06-model-infer/ims-06-lesson-02/)

Using the R package **infer**, a full randomization test for the statistical significance of the slope parameter is performed. Additionally, a bootstrap analysis is done to find a confidence interval for the slope.

### 3 - [t-test for slope](https://bghammill.github.io/ims-06-model-infer/ims-06-lesson-03/)

In contrast to the computational methods in the previous lesson, here you will use the t-distribution as a mathematical model for both statistical significance of the slope as well as confidence interval for the slope. The confidence interval representing an average response (at given value of x) is contrasted with a prediction interval representing an individual response (at a given value of x).

### 4 - [Checking technical conditions for slope inference](https://bghammill.github.io/ims-06-model-infer/ims-06-lesson-04/)

In order for the t-based methods (in the previous lesson) to produce meaningful results, it is important to ensure that the data (and underlying population structure) is set-up in a way consistent with the linear model specifications. Fortunately, there are adjustments to the structure (e.g., variable transformations) that can be made so that the technical conditions hold and produce meaningful conclusions (and are described in the following lesson).

### 5 - [Inference beyond the simple linear regression model](https://bghammill.github.io/ims-06-model-infer/ims-06-lesson-05/)

Building on the simple linear regression model (i.e., the model with only one explanatory variable), you will transform variables to produce models which are more characteristic of the underlying regression structure. Additionally, multiple variables will be considered, and interpretations of the variables??? coefficients will be examined.

