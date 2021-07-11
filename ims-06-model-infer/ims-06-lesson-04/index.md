
## Technical Conditions in linear regression

Additionally, you will consider the technical conditions that are important when using linear models to make claims about a larger population.

## Technical conditions for linear regression

In the previous lesson you saw that sometimes the mathematical model was not appropriate for inferential analysis (that is, for calculating p-values and confidence intervals).  In this lesson, we'll provide details for when the mathematical model is appropriate.

### What are the technical conditions?



$$Y = \beta_0 + \beta_1 \cdot X + \epsilon$$

$$\epsilon \sim N(0, \sigma_\epsilon)$$ 

* L: __linear__ model 
* I: __independent__ observations 
* N: points are __normally__ distributed around the line 
* E: __equal__ variability around the line for all values of the explanatory variable 


```
ggplot(lineardata, aes(x=explanatory, y=response)) + geom_point() +
  stat_smooth(method="lm", se=FALSE)
```

Because your goal in this tutorial is to perform inferential calculations on the linear regression model, it is important that the sampling distribution for the estimated slope has the expected form.  That is, we will be able to apply our methods only if the points are linear, independent, normally distributed, and have equal variability around the line.  Note that the conditions are given by the linear model equation as well as spelled out using the LINE mnemonic.


If the sampling distribution isn't accurate, the p-values and confidence intervals that you calculate could be wrong.



### Linear model: residuals


```
linear_lm <- augment(
  lm(response ~ explanatory,
     data = lineardata)
)

ggplot(linear_lm, 
       aes(x =. fitted, 
           y = .resid)) +
  geom_point() +
  geom_hline(yintercept=0)
```


fitted value: 
$\hat{Y}_i = b_0 + b_1 X_i$

residual:
$e_i= Y_i - \hat{Y}_i$


```
ggplot(linear_lm, aes(x=.fitted, y=.resid)) + geom_point() +
  geom_hline(yintercept=0)
```

The `augment` function in the **broom** package calculates the fitted and residual values for every point in the dataset.  The output of the `augment` function defaults to `.fitted` and `.resid`.

If the linear model is appropriate, a plot of the residuals versus the fitted values should show a non-patterned scattering of the points.  The fitted model is usually described by Roman letters (b0 and b1), whereas the population model we want to find is described by Greek letters (beta0 and beta1).

The residual plot here (fitted value plotted on the x-axis, residual values plotted on the y-axis) shows a scattering of points which do not indicate any violation of the regression technical conditions.



### Not linear


$$Y = \beta_0 + \beta_1 \cdot X + \epsilon$$

$$\epsilon \sim N(0, \sigma_\epsilon)$$

* L: linear model
* I: independent observations
* N: points are normally distributed around the line
* E: equal variability around the line for all values of the explanatory variable


```
ggplot(nonlineardata, aes(x=explanatory, y=response)) + geom_point()
```

The plot here demonstrates a clear violation of the linear model.  The variables have a quadratic relationship, not a linear one!



### Not linear: residuals


```
nonlinear_lm <- augment(
  lm(response ~ explanatory, data = nonlineardata)
  )

ggplot(nonlinear_lm, 
       aes(x = .fitted, y = .resid)) + 
  geom_point() +
  geom_hline(yintercept=0)
```


fitted value: 
$\hat{Y}_i = b_0 + b_1 X_i$

residual:
$e_i= Y_i - \hat{Y}_i$


The residuals associated with the quadratic model also look curved.  For the technical conditions to hold, you need a non-patterned scattering of points.  Just like the original scatter plot, the residual plot with fitted value on the x-axis, and residual on the y-axis continues to demonstrates a violation of the linear technical condition.



### Not normal




$$Y = \beta_0 + \beta_1 \cdot X + \epsilon$$

$$\epsilon \sim N(0, \sigma_\epsilon)$$

* L: linear model
* I: independent observations
* N: points are normally distributed around the line
* E: equal variability around the line for all values of the explanatory variable

```
ggplot(nonnormaldata, aes(x=explanatory, y=response)) + 
  geom_point() +
  stat_smooth(method="lm", se=FALSE)
```

The violation here is not as obvious as the non-linear violation.  In this plot, the points are not normally distributed around the line.  That is, although the residuals are centered at zero, the points under the line are closer to the line and the points above the line are scattered farther from the line.



### Not normal: residuals


```
nonnormal_lm <- augment(
  lm(response ~ explanatory, data = nonnormaldata)
  )

ggplot(nonnormal_lm, 
      aes(x = .fitted, y = .resid)) + 
  geom_point() +
  geom_hline(yintercept = 0) 
```
fitted value: 
$\hat{Y}_i = b_0 + b_1 X_i$

residual:
$e_i= Y_i - \hat{Y}_i$


The residual plot makes it even easier to see the violation of the technical condition related to normality.  If the residuals were normally distributed around the line, they would be equally far from the line in the positive and negative direction.  Here, the points below the line do not spread out nearly as far as the points above the line.



### Not equal variance


$$Y = \beta_0 + \beta_1 \cdot X + \epsilon$$

$$\epsilon \sim N(0, \sigma_\epsilon)$$

* L: linear model
* I: independent observations
* N: points are normally distributed around the line
* E: equal variability around the line for all values of the explanatory variable


```
ggplot(nonequaldata, aes(x=explanatory, y=response)) + geom_point() +
  stat_smooth(method="lm", se=FALSE)
```


The last violation we will investigate is unequal variability across different values of the explanatory variable.  In this plot, it seems as though the Y values associated with small X are quite close to the line whereas Y values associated with large values of X have a much larger variability around the line.



### Not equal variance: residuals

```
nonequal_lm <- augment(
  lm(response ~ explanatory,  data = nonequaldata) 
  )

ggplot(nonequal_lm, 
       aes(x = .fitted, y = .resid)) + 
  geom_point() +
  geom_hline(yintercept = 0)
```

fitted value:

$\hat{Y}_i = b_0 + b_1 X_i$

residual:

$e_i= Y_i - \hat{Y}_i$


Once again, the residual plot accentuates the technical condition violation by demonstrating the increasing variability of the residuals around the line as the fitted value increases.

###  

As mentioned previously, meeting the technical conditions will help to ensure that your p-values and confidence interval estimates are an accurate reflection of your population values.  Up soon, you will practice transforming data so as to meet the conditions.  But for now, it is your turn to practice determining when the technical conditions have been met.


### Violation of LINE conditions (1)


Which of the linear regression technical conditions are violated in the given figure?


```
require(mosaic)
require(ggplot2)
n = 100
beta0 = 10
beta1 = 3
x = runif(n)
e = rnorm(n)
ds = data.frame(y = beta0 + beta1 * x + 15*x^6 + e)
ggplot(ds, aes(x=x, y=y)) + geom_point()
```

Hint: These data do violate one of the technical conditions for *linear* regression.

```
question("Which of the linear regression technical conditions are violated in the given figure?",
  answer("*L*inearity",correct = TRUE, message="Right!  The variables do not show a linear relationship"),
  answer("*I*ndependence of errors", message="No, this condition seems fine.  Try again."),
  answer("*N*ormality of the response variable around the line", message="No, this condition seems fine.  Try again."),
  answer("*E*qual variability around the entire line", message="No, this condition seems fine.  Try again."),
  answer("No technical conditions are violated", message="The technical conditions are violated."),
  allow_retry = TRUE
)
```



### Violation of LINE conditions (2)


Which of the linear regression technical conditions are violated in the given figure?



```
require(mosaic)
require(ggplot2)
n = 100
beta0 = 10
beta1 = 3
x = runif(n)
e = rnorm(n)
ds = data.frame(y = beta0 + beta1 * x + e*x)
ggplot(ds, aes(x=x, y=y)) + geom_point()
```


Hint: These data do violate one of the conditions for linear regression. Look at the spread of the points around the line.


```
question("Which of the linear regression technical conditions are violated in the given figure?",
  answer("*L*inearity", message="No, this condition seems fine.  Try again."),
  answer("*I*ndependence of errors", message="No, this condition seems fine.  Try again."),
  answer("*N*ormality of the response variable around the line", message="No, this condition seems fine.  Try again."),
  answer("*E*qual variability around the entire line", message="Right!  The variability around the line increases as the explanatory variable (X) increases.", correct=TRUE),
  answer("No technical conditions are violated", message="The technical conditions are violated."),
  allow_retry = TRUE
)
```





### Using residuals (1)


In the next few exercises, you will calculate residuals from a data set that complies with the linear regression technical conditions.  For the linear model conditions to hold, the points should be scattered throughout the residual plot with no discernible pattern.  Here, the residual plot looks like a scattering of points.


```
set.seed(4747)
hypdata_nice <- tibble(explanatory = rnorm(200, 3, 1), 
                       response = explanatory * 3 + 12 + rnorm(200, 0, 2))
```




Using `hypdata_nice`, draw a scatter plot of `response` versus `explanatory`.



```
# Using hypdata_nice, draw a scatter plot of response vs. explanatory
___
```

```
- Call `ggplot()` with `hypdata_nice` as the data.
- Inside `aes()`, map `x` to `explanatory` and `y` to `response`.
- Add `geom_point()`.
```


```
# Using hypdata_nice, draw a scatter plot of response vs. explanatory
ggplot(hypdata_nice, aes(x = explanatory, y = response)) + 
  geom_point()
```






- Run a linear regression of `response` versus `explanatory` on `hypdata_nice`.
- Get the observation-level information from the model using `augment`.
- Using `modeled_observations`, plot `.resid` vs. `.fitted` and add a point layer.


```
# Run a linear regression of response vs. explanatory
model <- ___

# Augment to get the observation-level information
modeled_observations <- ___

# See the result
modeled_observations

# Using modeled_observations, draw a scatter plot of .resid vs. .fitted
___
```


```
- Call `lm()`, modeling `response` versus `explanatory` and setting the data to `hypdata_nice`.
- Call `augment()` to get the observation-level information from the model.
- Use `modeled_observations` as `ggplot()`'s data argument. Inside `aes()`, map `x` to `.fitted` and `y` to `.resid`.
- Add `geom_point()`.
```




```
# Run a linear regression of response vs. explanatory
model <- lm(response ~ explanatory, data = hypdata_nice)

# Augment to get the observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, draw a scatter plot of .resid vs. .fitted
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) + 
  geom_point()
```




### Using residuals (2)



Now, you will calculate residuals from a data set that violates the technical conditions.  For the linear model conditions to hold, the points should be scattered throughout the residual plot with no discernible pattern.  Here the residuals reveal a violation of the technical conditions.


```
set.seed(4747)
hypdata_poor <- tibble(explanatory = rnorm(200, 3, 1), response = explanatory * 3 + 12 + explanatory * rnorm(200, 0, 1))
```





Using `hypdata_poor`, draw a scatter plot of `response` versus `explanatory`.



```
# Using hypdata_poor, draw a scatter plot of response vs. explanatory
___
```


```
- Call `ggplot()` with `hypdata_poor` as the data.
- Inside `aes()`, map `x` to `explanatory` and `y` to `response`.
- Add `geom_point()`.
```


```
# Using hypdata_poor, draw a scatter plot of response vs. explanatory
ggplot(hypdata_poor, aes(x = explanatory, y = response)) + 
  geom_point()
```







- Run a linear regression of `response` versus `explanatory` on `hypdata_poor`.
- Get the observation-level information from the model using `augment`.
- Using `modeled_observations`, plot residuals vs. fitted values and add a point layer.



```
# Run a linear regression of response vs. explanatory
model <- ___

# Augment to get the observation-level information
modeled_observations <- ___

# See the result
modeled_observations

# Using modeled_observations, draw a scatter plot 
# of residuals vs. fitted values
___
```



```
- Call `lm()`, modeling `response` versus `explanatory` and setting the data to `hypdata_poor`.
- Call `augment()` to get the observation-level information from the model.
- Use `modeled_observations` as `ggplot()`'s data argument. Inside `aes()`, map `x` to `.fitted` and `y` to `.resid`.
- Add `geom_point()`.
```


```
# Run a linear regression of response vs. explanatory
model <- lm(response ~ explanatory, data = hypdata_poor)

# Augment to get the observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, draw a scatter plot 
# of residuals vs. fitted values
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) + 
  geom_point()
```



### Why do we need the LINE assumptions?


So far, you have implemented two approaches for performing inference assessment to a linear model.  The first way is given by the standard R output (`lm`) and is based on the t-distribution.  The derivation of the t-distribution is based on the theory (i.e., the LINE conditions).  

The second method uses a randomization test which assumes that the observations are exchangeable under the null hypothesis. That is, when the null hypothesis (X is independent of Y) is true, the Y values can be swapped among the X values.  The technical conditions in the randomization setting are linear relationship, independent observations, and equal variances.  However, the normality assumption is not needed.

What happens if inferences is performed when the technical conditions are violated?

Hint: More than one thing can go wrong when the technical conditions for regression are violated.

```
question("What happens if inferences is performed when the technical conditions are violated?",
  answer("If the technical conditions are violated, the software will not compute a p-value", message="The software will always provide a p-value, even if the number is meaningless"),
  answer("If the technical conditions are violated, the p-value will not represent the probability of the data given the null hypothesis is true.", message="True... but there is more."),
  answer("If the technical conditions are violated, the CI procedure will not capture the true parameter in 95% of samples.", message="True... but there is more."),
  answer("All of the above.", message="No. The p-value is always calculated, regardless of whether the distribution is appropriate."),
  answer("Some of the above.", message="Yes, the probabilities for both the p-value and the CI are based an accurate sampling distribution.", correct=TRUE),
  allow_retry = TRUE
)
```

## Effect of an outlier


Just as violating technical conditions can impact the accuracy of a p-value, one or a handful of outlying values can also have an unintended impact on the regression inferential procedure.



```
ggplot(starbucks, aes(x=Fiber, y=Protein)) + 
  geom_point() +
  stat_smooth(method="lm", se=FALSE) + 
  geom_point(data=subset(starbucks, Fiber > 15), color="red", cex=5) + 
  ggtitle("Fiber vs. protein")
```

Recall the linear model regressing protein on fiber.  You may have noticed previously that there was one food item with quite a bit of fiber and relatively little protein.

Additionally, maybe it turns out that you'd like to model only foods with relatively low fiber.  That is, if we remove the high fiber food, we can create a linear model which describes the relationship between fiber and protein only for foods that have less than 15g of fiber.



### Different regression lines

```

ggplot(starbucks, aes(x=Fiber, y=Protein)) + 
  geom_point() +
  stat_smooth(method="lm", se=FALSE) + 
  ggtitle("Linear model with full data") +
  xlim(c(0,22))
```

```
ggplot(data=subset(starbucks, Fiber < 15), aes(x=Fiber, y=Protein)) + 
  geom_point() +
  stat_smooth(method="lm", se=FALSE, col="red", fullrange = TRUE) + 
  ggtitle("Linear model with low fiber foods") +
  xlim(c(0,22))
```

The decision of whether or not to keep the high fiber food will have an impact on the regression line describing the relationship between protein and fiber.  Notice that by removing the the outlier, the regression line changes from the original least squares estimate.



### Different regression lines


```
ggplot(starbucks, aes(x=Fiber, y=Protein)) + geom_point() +
  stat_smooth(method="lm", se=FALSE, ) +
  stat_smooth(data = starbucks_lowFib, method="lm", se=FALSE, color="red", fullrange = TRUE) +
  ggtitle("Both linear models")
```

The two regression lines have been superimposed so that the change in relationship is easier to see.  Keep in mind that the red line models foods with less than 15g of fiber because we have subsetted the explanatory variable.  We would never remove a data point simply because it didn't fit a particular model, we only remove the point if we are interested in describing only a subset of the observations.



### Different regression models


```
starbucks_lowFib <- starbucks %>% filter(Fiber < 15)

lm(Protein ~ Fiber, data = starbucks) %>% tidy()
#          term estimate std.error statistic      p.value
# 1 (Intercept) 7.526138 0.9924180  7.583637 1.101756e-11
# 2       Fiber 1.383684 0.2451395  5.644476 1.286752e-07


lm(Protein ~ Fiber, data = starbucks_lowFib) %>% tidy()
#          term estimate std.error statistic      p.value
# 1 (Intercept) 6.537053 1.0633640  6.147521 1.292803e-08
# 2       Fiber 1.796844 0.2995901  5.997675 2.600224e-08
```


In this code, we use the original dataset as well as the dataset which is filtered to include only values for which Fiber is less than 15.  The linear model is given for both datasets.

As expected, the values of the slope and intercept change depending on which data values are included in their calculations.  And although the p-value given for the statistical inference is statistically significant in both situations, the p-values are different by a factor of 5.



### Different regression randomization tests


### Full data set
```
perm_slope %>% mutate(
    abs_perm_slope = abs(stat)
) %>%
  summarize(
    p_value = mean(
      abs_perm_slope > abs(obs_slope)
    )
  )

#  A tibble: 1 x 1
#   p_value
#     <dbl>
# 1       0
```



### Low fiber data set
```
perm_slope_lowFib %>% mutate(
  abs_perm_slope = abs(stat)
) %>%
  summarize(
    p_value = mean(
      abs_perm_slope > 
      abs(obs_slope_lowFib)
     )
   )
#  A tibble: 1 x 1
#   p_value
#     <dbl>
# 1       0
```

Similar to the mathematical analysis, the randomization test on the slope can be run for the full dataset or the low-fiber dataset.  Again, both tests are well into the statistically significant range.

### Estimation with and without outlier

Remember, never remove points from the dataset unless you know the observed value to be incorrect or unless you are modeling a subset of your explanatory variable.

The data provided in this exercise (`hypdata_outlier`) has an extreme outlier.  A plot is shown of the dataset, and a linear regression model of `response` versus `explanatory`. You will remove the outlying point to see how one observation can affect the estimate of the line.


- Filter `hypdata_outlier` to remove the outlier.
- Update the plot, `p`, to add another smooth layer (use `geom_smooth`). 
    - Like the other ribbon, the update should use the linear regression method, and not draw the ribbon.
    - Unlike the other ribbon, the update should use the `data = hypdata_no_outlier` and be colored red.
    - For now, just use the smooth curve, and not the confidence bounds (`se = FALSE`).



```
set.seed(4747)
hypdata_outlier <- tibble(explanatory = rnorm(200, 3, 1), response = explanatory * 3 + 12 + rnorm(200, 0, 2))
hypdata_outlier$response[47] <- 500

ggplot(hypdata_outlier, aes(x = explanatory, y = response)) + 
  geom_point() +
  geom_smooth(method = "lm", se = FALSE) 
```



```
# This plot is shown
p <- ggplot(hypdata_outlier, aes(x = explanatory, y = response)) + 
  geom_point() +
  geom_smooth(method = "lm", se = FALSE) 

# Filter to remove the outlier
hypdata_no_outlier <- ___

p +
  # Add another smooth lin .reg. layer, no ribbon, 
  # hypdata_no_outlier data, colored red
  ___
```


```
- You can filter on `response` being less than `200`, for example.
- Add `geom_smooth()`, setting `method` to `"lm"`, `se` to `FALSE`, `data` to `hypdata_no_outlier`, and `color` to `"red"`.
```

```
# This plot is shown
p <- ggplot(hypdata_outlier, aes(x = explanatory, y = response)) + 
  geom_point() +
  geom_smooth(method = "lm", se = FALSE) 

# Filter to remove the outlier
hypdata_no_outlier <- hypdata_outlier %>% 
  filter(response < 200)

p +
  # Add another smooth lin .reg. layer, no ribbon, 
  # hypdata_no_outlier data, colored red
  geom_smooth(
    method = "lm", se = FALSE, 
    data = hypdata_no_outlier, color = "red"
  )
```




### Inference with and without outlier (t-test)


Not only can one point change the estimated regression line, but it can also change the inferential analysis.

The datasets with and without the outlier are provided as `hypdata_outlier` and `hypdata_no_outlier` respectively.


- Run a linear regression of `response` vs. `explanatory` on `hypdata_outlier`, and extract the coefficient-level information.
- Do the same for `hypdata_no_outlier`.
- Look at the output. How do the coefficients and p-values differ?



```
set.seed(4747)
hypdata_outlier <- tibble(explanatory = rnorm(200, 3, 1), response = explanatory * 3 + 12 + rnorm(200, 0, 2))
hypdata_outlier$response[47] <- 500
hypdata_no_outlier <- hypdata_outlier %>% 
  filter(response < 200)
```


```
# Model response vs. explanatory on hypdata_outlier and tidy it
___

# Do the same on hypdata_no_outlier
___
```


```
- Call `lm()`, modeling `response` vs. `explanatory`, for each dataset.
- Call `tidy()` on each model to get the coefficient-level information.
```

```
# Model response vs. explanatory on hypdata_outlier and tidy it
tidy(lm(response ~ explanatory, data = hypdata_outlier))

# Do the same on hypdata_no_outlier
tidy(lm(response ~ explanatory, data = hypdata_no_outlier))
```



### Inference with and without outlier (randomization)



Using the randomization test, you can again evaluate the effect of an outlier on the inferential conclusions of a linear model.  Run a randomization test on the `hypdata_out` data twice:  once with the outlying value and once without it.  Note that the extended lines of code communicate clearly the steps of the randomization tests.


Using the data frames `hypdata_out` (containing an outlier) and `hypdata_noout` (outlier removed), the data frames `perm_slope_out` and `perm_slope_noout` were created to contain the permuted slopes the original datasets, respectively. The observed values are stored in the variables `obs_slope_out` and `obs_slope_noout`.

-  Find the p-values by finding the proportion of ( `abs`olute value) permuted slopes which are larger than or equal to the ( `abs`olute value of the) observed slopes.  As before, use `mean` on the binary inequality to find the proportion.


```
set.seed(4747)
explanatory <- rnorm(50,3,1)
response <- explanatory*3 + 12 + rnorm(50, 0, 10)
response[47] <- 500
hypdata_out <-  data.frame(explanatory, response)

obs_slope_out <- lm(response ~ explanatory, hypdata_out) %>%
  tidy() %>%
  filter(term == "explanatory") %>%
  select(estimate) %>%
  pull() 

hypdata_noout <- hypdata_out %>% filter(explanatory < 4.6) 

obs_slope_noout <- lm(response ~ explanatory, hypdata_noout) %>%
  tidy() %>%
  filter(term == "explanatory") %>%
  select(estimate) %>%
  pull() 

set.seed(4747)
perm_slope_out <- hypdata_out %>%
  specify(response ~ explanatory) %>%
  hypothesize("independence") %>%
  generate(reps=500, type="permute") %>%
  calculate(stat = "slope")

set.seed(4747)
perm_slope_noout <- hypdata_noout %>%
  specify(response ~ explanatory) %>%
  hypothesize("independence") %>%
  generate(reps=500, type="permute") %>%  
  calculate(stat = "slope")
```


```
# Calculate the p-value with the outlier
perm_slope_out %>% 
  mutate(abs_perm_slope = ___) %>%
  summarize(p_value = ___)

# Calculate the p-value without the outlier
perm_slope_noout %>% 
  mutate(abs_perm_slope = ___) %>%
  summarize(p_value = ___)
```



```
Repeat the same procedure twice and compare the p-values.
```



```
# Calculate the p-value with the outlier
perm_slope_out %>% 
  mutate(abs_perm_slope = abs(stat)) %>%
  summarize(p_value = mean(abs_perm_slope >= abs(obs_slope_out)))

# Calculate the p-value without the outlier
perm_slope_noout %>% 
  mutate(abs_perm_slope = abs(stat)) %>%
  summarize(p_value = mean(abs_perm_slope >= abs(obs_slope_noout)))
```


## Moving forward when model assumptions are violated


Before we embark on what to do when the model technical assumptions are violated, I'd like to remind you that you won't get accurate models or analyses if you remove outlying data points on a whim.  If you want a model built on data that describes a population, you have to use all the data.  In the previous example, however, we removed data values that were outside the range of interest.  That is, we only considered points with fiber less than 15g. You removed points that were less than 4.6 in your hypothetical scenario — effectively subsetting the explanatory region that you describe.  It will be important to explain such subsetting in the final analysis report.



### Linear Model



$$Y = \beta_0 + \beta_1 \cdot X + \epsilon$$ 

where $\epsilon \sim N(0, \sigma_\epsilon)$


Recall the linear model that we've been working with.  Note that X and Y have a linear relationship and the noise term gets added on.  Also, recall that the noise values come from a normal distribution centered around zero.  Transforming either the explanatory (X) or response (Y) variables will change the entire form of the model.  

In order to perform an inferential analysis to describe the variables, the technical conditions must be met.  That means, we need a linear relationship between x and y.  Sometimes, there doesn't exist a linear relationship between x and y but there does exist a linear relationship between a function of x or a function of y.

### Transforming the explanatory variable

$Y = \beta_0 + \beta_1 \cdot X + \beta_2 \cdot X^2 + \epsilon$, where $\epsilon \sim N(0, \sigma_\epsilon)$

$Y = \beta_0 + \beta_1 \cdot \ln(X) + \epsilon$, where $\epsilon \sim N(0, \sigma_\epsilon)$

$Y = \beta_0 + \beta_1 \cdot \sqrt{X} + \epsilon$, where $\epsilon \sim N(0, \sigma_\epsilon)$

When the data come from a model which is a function of the explanatory variable, the model stays linear.  However, it becomes linear in a function of X instead of linear in X itself.  We say: y is a linear function of x and x-squared (together) or y is a linear function of the log of x.  ... of the square root.

But importantly, changing the values of X do not change the values of the residuals, as they are still modeled to be normally distributed and centered around zero with constant variance.

### Squaring the explanatory variable

```
ggplot(data=data_nonlin, 
  aes(x=explanatory, y=response)) + 
  geom_point() +
  ggtitle("Non-linear data")
```


```
ggplot(data=data_nonlin, 
  aes(x=explanatory^2, y=response))+ 
     geom_point() +
  ggtitle("Square transformation on explanatory variable")
```



When the true model is given by Y versus the squareroot of X, we can linearize the model by squaring X, the explanatory variable.  Note that the scatterplot on the right uses the square of the explanatory variable.  Squaring the input variable is great for modeling data that are better fit by a curved line.


### Transforming the response variable


$Y^2 = \beta_0 + \beta_1 \cdot X + \epsilon$, where $\epsilon \sim N(0, \sigma_\epsilon)$

$\ln(Y) = \beta_0 + \beta_1 \cdot X + \epsilon$, where $\epsilon \sim N(0, \sigma_\epsilon)$

$\sqrt{Y} = \beta_0 + \beta_1 \cdot X + \epsilon$, where $\epsilon \sim N(0, \sigma_\epsilon)$


Unlike transformations on the explanatory variable, when a response variable is transformed, the relationship between the linear model and the error terms is also changed.



### A natural log transformation


```
ggplot(data=data_nonnorm, 
  aes(x=explanatory, y=response)) + 
     geom_point() +
  ggtitle("Non-linear and increasing variance data")
```



```
ggplot(data=data_nonnorm, 
  aes(x = explanatory,
      y = log(response))) + 
     geom_point() +
  ggtitle("Log transformation on response variable")
```


Notice that the scatterplot on the right uses the natural log of the response variable.  When the model is run after taking the log of the response variable, both the shape of the relationship and the increasing noise problem has been fixed!  Using the log transform on the response is good for data where the variance is unequal.


###  


With many different possible transformations, there is not always just one right way to transform the data.  For now, you will practice transforming variables and checking residual plots to determine whether the technical conditions are met.


### Adjusting for non-linear relationship



The next three examples work with datasets where the underlying data structure violates the linear regression technical conditions.  For each example, you will apply a transformation to the data in order to create residual plots that look scattered.

In this first example, it appears as though the variables are not linearly related.


```
n <- 100
set.seed(4747)
hypdata_nonlinear <- data_frame(
  explanatory = runif(n),
  response = 10 + 3 * explanatory + 15 * explanatory ^ 2 + rnorm(n)
)
```





- Run a linear regression of `response` versus `explanatory` on `hypdata_nonlinear`.
- Get the observation-level information from the model.
- Using `modeled_observations`, plot the residuals versus the fitted values.
    - Add a point layer.
    - Add a horizontal line at `y = 0` using `geom_hline()` and setting `yintercept` to `0`.



```
# Run this to see how the model looks
ggplot(hypdata_nonlinear, aes(x = explanatory, y = response)) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model response vs. explanatory 
model <- ___

# Extract observation-level information
modeled_observations <- ___

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
___ +
  # Add a point layer
  ___ + 
  # Add horizontal line at y = 0
  ___
```


```
- Call `lm()`, modeling `response` vs. `explanatory`, and setting `data` to `hypdata_nonlinear`.
- Call `augment()` on the model.
- Inside the ggplot's `aes()`, map `x`to `.fitted` and `y` to `.resid`.
- Add `geom_point()` then `geom_hline()`, the latter with `yintercept` set to `0`.
```


```
# Run this to see how the model looks
ggplot(hypdata_nonlinear, aes(x = explanatory, y = response)) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model response vs. explanatory 
model <- lm(response ~ explanatory, data = hypdata_nonlinear)

# Extract observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) +
  # Add a point layer
  geom_point() + 
  # Add horizontal line at y = 0
  geom_hline(yintercept = 0)
```




- Update the model add a quadratic term, `I(explanatory^2)`, to the right-hand side of the formula.
- Rerun the code and look at how the plots change.



```
# Run this to see how the model looks
ggplot(hypdata_nonlinear, aes(x = explanatory + explanatory ^ 2, y = response)) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model response vs. explanatory plus explanatory squared
model <- lm(response ~ explanatory + ___, data = hypdata_nonlinear)

# Extract observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) +
  # Add a point layer
  geom_point() + 
  # Add horizontal line at y = 0
  geom_hline(yintercept = 0)
```



```
The right-hand side of the new formula should be `explanatory + I(explanatory ^ 2)`.
```

```
# Run this to see how the model looks
ggplot(hypdata_nonlinear, aes(x = explanatory + explanatory ^ 2, y = response)) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model response vs. explanatory plus explanatory squared
model <- lm(response ~ explanatory + I(explanatory ^ 2), data = hypdata_nonlinear)

# Extract observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) +
  # Add a point layer
  geom_point() + 
  # Add horizontal line at y = 0
  geom_hline(yintercept = 0)
```




### Adjusting for non-constant errors



In this next example, it appears as though the variance of the `response` variable increases as the `explanatory` variable increases.  Note that the fix in this exercise has the effect of changing both the variability as well as modifying the linearity of the relationship.


```

```





- Run a linear regression of `response` versus `explanatory` on `hypdata_nonequalvar`.
- Get the observation-level information from the model.
- Using `modeled_observations`, plot the residuals versus the fitted values.
    - Add a point layer.
    - Add a horizontal line at `y = 0` using `geom_hline()` and setting `yintercept` to `0`.



```
# Run this to see how the model looks
ggplot(hypdata_nonequalvar, aes(x = explanatory, y = response)) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model response vs. explanatory 
model <- ___

# Extract observation-level information
modeled_observations <- ___

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
___
```

```
- Call `lm()`, modeling `response` vs. `explanatory`, and setting `data` to `hypdata_nonequalvar`.
- Call `augment()` on the model.
- Inside the ggplot's `aes()`, map `x`to `.fitted` and `y` to `.resid`.
- Add `geom_point()` then `geom_hline()`, the latter with `yintercept` set to `0`.
```

```
# Run this to see how the model looks
ggplot(hypdata_nonequalvar, aes(x = explanatory, y = response)) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model response vs. explanatory 
model <- lm(response ~ explanatory, data = hypdata_nonequalvar)

# Extract observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) +
  # Add a point layer
  geom_point() + 
  # Add horizontal line at y = 0
  geom_hline(yintercept = 0)
```





- Update the model so that the left-hand side of the formula is `log(response)`.
- Rerun the code and look at how the plots change.


```
# Run this to see how the model looks
ggplot(hypdata_nonequalvar, aes(x = explanatory, y = log(response))) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model log-response vs. explanatory 
model <- lm(___ ~ explanatory, data = hypdata_nonequalvar)

# Extract observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) +
  # Add a point layer
  geom_point() + 
  # Add horizontal line at y = 0
  geom_hline(yintercept = 0)
```

```
The new formula should be `log(response) ~ explanatory`.
```


```
# Run this to see how the model looks
ggplot(hypdata_nonequalvar, aes(x = explanatory, y = log(response))) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model log-response vs. explanatory 
model <- lm(log(response) ~ explanatory, data = hypdata_nonequalvar)

# Extract observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) +
  # Add a point layer
  geom_point() + 
  # Add horizontal line at y = 0
  geom_hline(yintercept = 0)
```




### Adjusting for non-normal errors

In this last example, it appears as though the points are not normally distributed around the regression line.  Again, note that the fix in this exercise has the effect of changing both the variability as well as modifying the linearity of the relationship.


```
n <- 100
set.seed(4747)
hypdata_nonnorm = data_frame(
  explanatory = runif(n),
  response = (10 + 10 * explanatory + rnorm(n, 0, 4)) ^ 2
)
```





- Run a linear regression of `response` versus `explanatory` on `hypdata_nonnorm`.
- Get the observation-level information from the model.
- Using `modeled_observations`, plot the residuals versus the fitted values.
    - Add a point layer.
    - Add a horizontal line at `y = 0` using `geom_hline()` and setting `yintercept` to `0`.


```
# Run this to see how the model looks
ggplot(hypdata_nonnorm, aes(x = explanatory, y = response)) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model response vs. explanatory 
model <- ___

# Extract observation-level information
modeled_observations <- ___

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
___
```


```
- Call `lm()`, modeling `response` vs. `explanatory`, and setting `data` to `hypdata_nonnorm`.
- Call `augment()` on the model.
- Inside the ggplot's `aes()`, map `x`to `.fitted` and `y` to `.resid`.
- Add `geom_point()` then `geom_hline()`, the latter with `yintercept` set to `0`.
```

```
# Run this to see how the model looks
ggplot(hypdata_nonnorm, aes(x = explanatory, y = response)) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model response vs. explanatory 
model <- lm(response ~ explanatory, data = hypdata_nonnorm)

# Extract observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) +
  # Add a point layer
  geom_point() + 
  # Add horizontal line at y = 0
  geom_hline(yintercept = 0)
```






- Update the model so that the left-hand side of the formula is the square root of `response`.
- Rerun the code and look at how the plots change.


```
# Run this to see how the model looks
ggplot(hypdata_nonnorm, aes(x = explanatory, y = sqrt(response))) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model response vs. explanatory 
model <- lm(___ ~ explanatory, data = hypdata_nonnorm)

# Extract observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) +
  # Add a point layer
  geom_point() + 
  # Add horizontal line at y = 0
  geom_hline(yintercept = 0)
```



```
The new formula should be `sqrt(response) ~ explanatory`.

```


```
# Run this to see how the model looks
ggplot(hypdata_nonnorm, aes(x = explanatory, y = sqrt(response))) + 
  geom_point() + 
  geom_smooth(method = "lm", se = FALSE)

# Model response vs. explanatory 
model <- lm(sqrt(response) ~ explanatory, data = hypdata_nonnorm)

# Extract observation-level information
modeled_observations <- augment(model)

# See the result
modeled_observations

# Using modeled_observations, plot residuals vs. fitted values
ggplot(modeled_observations, aes(x = .fitted, y = .resid)) +
  # Add a point layer
  geom_point() + 
  # Add horizontal line at y = 0
  geom_hline(yintercept = 0)
```

## Congratulations!

You have successfully completed Lesson 4 in Tutorial 8: Inference for Regression.  

What's next?

`r emo::ji("ledger")` [Full list of tutorials supporting OpenIntro::Introduction to Modern Statistics](https://openintrostat.github.io/ims-tutorials/)

`r emo::ji("spiral_notepad")` [Tutorial 8: Inference for Regression](https://openintrostat.github.io/ims-tutorials/08-inference-for-regression/)

`r emo::ji("one")` [Tutorial 8 - Lesson 1: Inference in regression](https://openintro.shinyapps.io/ims-08-inference-for-regression-01/)

`r emo::ji("one")` [Tutorial 8 - Lesson 2: Randomization test for slope](https://openintro.shinyapps.io/ims-08-inference-for-regression-02/)

`r emo::ji("one")` [Tutorial 8 - Lesson 3: t-test for slope](https://openintro.shinyapps.io/ims-08-inference-for-regression-03/)

`r emo::ji("one")` [Tutorial 8 - Lesson 4: Checking technical conditions for slope inference](https://openintro.shinyapps.io/ims-08-inference-for-regression-04/)

`r emo::ji("one")` [Tutorial 8 - Lesson 5: Inference beyond the simple linear regression model](https://openintro.shinyapps.io/ims-08-inference-for-regression-05/)

`r emo::ji("open_book")` [Learn more at Introduction to Modern Statistics](http://openintro-ims.netlify.app/)