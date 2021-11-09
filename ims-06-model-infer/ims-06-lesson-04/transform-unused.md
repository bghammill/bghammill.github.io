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

