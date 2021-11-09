






















## Different types of Intervals

Throughout the different tutorials, you have repeatedly seen confidence intervals which attempt to capture the true population parameter of interest. In the previous exercises, you created confidence intervals for the true slope and intercept parameters.

However, in regression, it is often of interest to put an interval estimate onto the average response or the predicted response variable.



### Fat & calories, a linear model

```
starbucks %>%   ggplot(aes(x=Fat, y=Calories)) + geom_point() +   ggtitle("Fat vs. Calories for Starbucks Food Items") +  geom_smooth(method="lm", se=FALSE)
```


Recall that the variability of the linear model is based on both the slope and intercept changing from sample to sample.



### Fat & calories, many linear models


```
ggplot(starbucks_many, aes(x=Fat, y=Calories, group=replicate)) +   geom_point() +   ggtitle("Fat vs. Calories for Starbucks Food Items") +   geom_smooth(method="lm", se=FALSE)
```


Indeed, the variability of the prediction is wider at the extreme values of fat. That is, it is harder to predict the average calories of food items that have zero fat than those that have 20g of fat.



### Predicting average calories at specific fat

```
alpha <- .05crit_val <- qt((1-alpha/2), df = nrow(starbucks) - 2) newfood <- data.frame(Fat = c(0,10,20,30)) augment(lm(Calories ~ Fat, data=starbucks),         se_fit = TRUE,        newdata = newfood) %>%   mutate(lowMean = .fitted - crit_val*.se.fit,          upMean = .fitted + crit_val*.se.fit)
```

The `augment` function (in the broom package) calculates the standard error for the predicted average at each separate explanatory variable of interest (`.se.fit`). the variability at zero and 30 grams of fat is much higher than the variability at 10 or 20 grams of fat. 

As before, the CI for the *average* calories (as a parameter value) is the statistic plus or minus the standard error times the critical value. The critical value (`crit_val`) here is calculated for a 95% interval with degrees of freedom of the total number of observations minus two. 

Notice that the CI (`118.3 to 177.7`) at zero grams of fat is the same as the CI for the intercept. 

The 95% confidence interval (`258.7 to 292.4`) representing the average calorie content for foods with 10g of fat is 258.7 calories to 292.4 calories.

Additionally, you can see the intervals which capture the average calorie content with 95% confidence for foods that are 20g of fat (`388 and 418`) and 30g of fat (`505 to 556.7`).



### Creating CI for average response


```
predMeans <- augment(lm(Calories ~ Fat, data = starbucks),                     se_fit = TRUE) %>%   select(Calories, Fat, .fitted, .se.fit) %>%   mutate(lowMean = .fitted - crit_val*.se.fit,          upMean =  .fitted + crit_val*.se.fit) head(predMeans)
```


In order to produce a confidence bound for the entire linear model, the interval is calculated for every observation in the dataset.



### Plotting CI for average response


```
ggplot(predMeans, aes(x = Fat, y = Calories)) +    geom_point() +   stat_smooth(method = "lm", se = FALSE) +    geom_ribbon(aes(ymin = lowMean, ymax = upMean), alpha=.2)
```


In order to produce a confidence bound for the entire linear model, the interval is calculated for every observation in the dataset. We have plotted the interval using the `geom_ribbon`  in ggplot2, but the same plot could have been created using `se = TRUE` in the stat smooth call.



### Prediction intervals


```
alpha <- .05crit_val <- qt((1-alpha/2), df = nrow(twins) - 2)FatCal_lm <- lm(Calories ~ Fat, data = starbucks)FatCal_gl <- glance(FatCal_lm)FatCal_sig <- pull(FatCal_gl, sigma)FatCal_pred <- augment(FatCal_lm,                       se_fit = TRUE) %>%  mutate(.se.pred = sqrt(FatCal_sig^2 + .se.fit^2))predResp <- FatCal_pred %>%  mutate(lowResp = .fitted - crit_val*.se.pred,          upResp = .fitted + crit_val*.se.pred)predResp
```


Prediction intervals give a range of plausible values for the individual observations at a given level of the explanatory variable. For example, a prediction interval will tell the calorie counts for 95% of Starbucks foods with 10g of fat. 

Note that the difference between prediction intervals for individual responses and confidence intervals for average responses comes in the value of the standard error, see ` mutate(.se.pred = sqrt(FatCal_sig^2 + .se.fit^2))`.

The standard error of the individual prediction is a combination of how variable the line is (`.se.fit`) and how variable the individual points are (`FatCal_sig`).



### Plotting prediction intervals


```
ggplot(predResp, aes(x = Fat, y = Calories)) +   geom_point() +  stat_smooth(method = "lm", se = FALSE) +   geom_ribbon(aes(ymin = lowResp, ymax = upResp), alpha = .2)
```


We plot the prediction interval bounds using the `geom_ribbon` function with the new interval upper and lower limits.

### Confidence intervals for the average response at specific values

The previous two exercises gave CIs for the slope parameter. That is, based on the observed data, you provided an interval estimate of the plausible values for the true slope parameter in the population. Recall that the number 1 was in the CI for the slope, meaning 1 cannot be ruled out as a possible value for the true slope between Biological and Foster twins. There is no evidence to claim that there is a difference, on average, between the IQ scores of two twins in any given pair.

When working with a linear regression model, you might also want to know the plausible values for the expected value of the response at a particular explanatory location. That is, what would you expect the IQ of a Foster twin to be given a Biological twin's IQ of 100?


```
alpha <- 0.05degrees_of_freedom <- nrow(twins) - 2confidence_level <- 1 - alphap_upper <- 1 - alpha / 2critical_value <- qt(p_upper, df = degrees_of_freedom)
```




Call `augment()` on `model` to see the observation-level data in the model.



```
model <- lm(Foster ~ Biological, data = twins)# Get observation-level values from the model___
```


```
Call `augment()`, passing `model` as the only input.
```


```
model <- lm(Foster ~ Biological, data = twins)# Get observation-level values from the modelaugment(model, se_fit = TRUE)
```






- Create a new twins dataset of Biological IQs to make predictions of Foster IQs on. Create a `data.frame` with a column named `Biological`, taking values as a `seq`uence from `70` to `130` in steps of `15`.
- Augment the model with the new dataset. Set `augment()`'s `newdata` argument to `new_twins`.

```
# From previous stepmodel <- lm(Foster ~ Biological, data = twins)# Create a dataframe of new observationsnew_twins <- ___# Augment the model with the new datasetaugmented_model <- ___# See the resultaugmented_model
```



```
- Call `data.frame()`, setting `Biological` to `70`, `85`, ..., `130`.- Call `augment()`, passing `model` and setting `newdata` to `new_twins`. Remember to set `se_fit = TRUE`.
```


```
# From previous stepmodel <- lm(Foster ~ Biological, data = twins)# Create a dataframe of new observationsnew_twins <- data.frame(Biological = seq(70, 130, 15))# Augment the model with the new datasetaugmented_model <- model %>%     augment(newdata = new_twins, se_fit = TRUE)# See the resultaugmented_model
```





Calculate a confidence interval of the predicted IQs of the foster twin, based on the augmented model. This is the predicted value, `.fitted`, plus or minus the critical value times the standard error of the prediction, `.se.fit`.



```
# From previous stepsmodel <- lm(Foster ~ Biological, data = twins)new_twins <- data.frame(Biological = seq(70, 130, 15))augmented_model <- model %>% augment(newdata = new_twins, se_fit = TRUE)augmented_model %>%  # Calculate a confidence interval on the predicted values  mutate(    lower_mean_prediction = ___,    upper_mean_prediction = ___  )
```


```
- Inside `mutate()`, calculate `lower_mean_prediction` as `.fitted` minus `critical_value` times `.se.fit`.- For `upper_mean_prediction`, change the minus to a plus.
```



```
# From previous stepsmodel <- lm(Foster ~ Biological, data = twins)new_twins <- data.frame(Biological = seq(70, 130, 15))augmented_model <- model %>% augment(newdata = new_twins, se_fit = TRUE)augmented_model %>%  # Calculate a confidence interval on the predicted values  mutate(    lower_mean_prediction = .fitted - critical_value * .se.fit,    upper_mean_prediction = .fitted + critical_value * .se.fit  )
```





### Confidence intervals for the average response for all observations



The confidence interval for the average response can be computed for all observations in the dataset. Using `augment()` directly on the `twins` dataset gives predictions and standard errors for the Foster twin based on all the Biological observations. 

Note that the calculation of the regression line is more stable at the center, so predictions for the extreme values are more variable than predictions in the middle of the range of explanatory IQs.

The foster twin IQ predictions that you calculated last time are provided as `predictions`. These predictions are shown in a plot using `geom_smooth()`.


Manually create what `geom_smooth()` does, using `predictions`. Provide the aesthetics and data to each geom.

- Add a point layer of `Foster` vs. `Biological`, using the `data = twins` dataset.
- Add a line layer of `.fitted` vs. `Biological`, using the `data = predictions` dataset. Color the line `"blue"`.
- Add a ribbon layer with `x` mapped to `Biological`, `ymin` mapped to `lower_mean_prediction` and `ymax` mapped to `upper_mean_prediction`. Use the `data = predictions` dataset and set the transparency, `alpha` to `0.2`.


```
alpha <- 0.05degrees_of_freedom <- nrow(twins) - 2confidence_level <- 1 - alphap_upper <- 1 - alpha / 2critical_value <- qt(p_upper, df = degrees_of_freedom)model <- lm(Foster ~ Biological, data = twins)new_twins <- data.frame(Biological = seq(70, 130, 15))augmented_model <- model %>% augment(newdata = new_twins, se_fit = TRUE)predictions <- augmented_model %>%  # Calculate a confidence interval on the predicted values  mutate(    lower_mean_prediction = .fitted - critical_value * .se.fit,    upper_mean_prediction = .fitted + critical_value * .se.fit  )ggplot(twins, aes(x = Biological, y = Foster)) +   geom_point() +  geom_smooth(method = "lm") 
```


```
# This plot is shownggplot(twins, aes(x = Biological, y = Foster)) +   geom_point() +  geom_smooth(method = "lm") ggplot() +   # Add a point layer of Foster vs. Biological, using twins  ___(aes(___, ___), data = ___) +  # Add a line layer of .fitted vs Biological, using predictions, colored blue  ___ +  # Add a ribbon layer of lower_mean_prediction to upper_mean_prediction vs Biological,   # using predictions, transparency of 0.2  ___
```





```
- Add `geom_point()`. Inside `aes()`, map `x` to `Biological` and `y` to `Foster`. Set `data` to `twins`.- Add `geom_line()`. Inside `aes()`, map `x` to `Biological` and `y` to `.fitted`. Set `data` to `predictions`.- Add `geom_ribbon()`. Inside `aes()`, map `x` to `Biological`, `ymin` to `lower_mean_prediction`, and `ymax` to `upper_mean_prediction`. Set `data` to `predictions`, and `alpha` to `0.2`.
```

```
# This plot is shownggplot(twins, aes(x = Biological, y = Foster)) +   geom_point() +  geom_smooth(method = "lm") ggplot() +   # Add a point layer of Foster vs. Biological, using twins  geom_point(aes(x = Biological, y = Foster), data = twins) +  # Add a line layer of .fitted vs Biological, using predictions, colored blue  geom_line(aes(x = Biological, y = .fitted), data = predictions, color = "blue") +  # Add a ribbon layer of lower_mean_prediction to upper_mean_prediction vs Biological,   # using predictions, transparency of 0.2  geom_ribbon(    aes(x = Biological, ymin = lower_mean_prediction, ymax = upper_mean_prediction),     data = predictions, alpha = 0.2  )
```






### Prediction intervals for the individual response



Along with an interval estimate for the expected value of the response, it is often desired to have an interval estimate for the actual individual responses. The formulation for the prediction is the same, but the predicted points are more variable around the line, so the standard error is calculated to be a larger value.

As with the interval around the expected average values, the interval for predicted individual values is smaller in the middle than on the extremes due to the calculation of the regression line being more stable at the center. Note that the intervals for the average responses are much smaller than the intervals for the individual responses.

You have already seen `tidy()`, to pull out coefficient-level information from a model, and `augment()` for observation-level information. `glance()` completes the triumvirate, giving you model-level information.

The linear regression is provided as `model` and the predictions from the previous exercise are given as `predictions`.


```
alpha <- 0.05degrees_of_freedom <- nrow(twins) - 2confidence_level <- 1 - alphap_upper <- 1 - alpha / 2critical_value <- qt(p_upper, df = degrees_of_freedom)model <- lm(Foster ~ Biological, data = twins)new_twins <- data.frame(Biological = seq(70, 130, 15))augmented_model <- model %>% augment(newdata = new_twins, se_fit = TRUE)predictions <- augmented_model %>%  # Calculate a confidence interval on the predicted values  mutate(    lower_mean_prediction = .fitted - critical_value * .se.fit,    upper_mean_prediction = .fitted + critical_value * .se.fit  )model <- lm(Foster ~ Biological, data = twins)
```




- Find the natural variability of the points around the prediction line.
  - Use `glance()` to get the model-level information from `model`. 
  - Pull out the `sigma` element.
- Calculate the standard error of the predictions as the square root of the sum of (`twins_sigma` squared and `.se.fit` squared).



```
twins_sigma <- model %>%  # Get model-level information  ___ %>%  # Pull out sigma  ___predictions %>%  # Calculate the std err of the predictions  mutate(std_err_of_predictions = ___)
```


```
- From `model`, pipe to `glance()` then `pull()`, passing `sigma` as the only argument to the latter.- Inside `mutate()`, `std_err_of_predictions` should be the `sqrt()` of (`twins_sigma` to the power `2`) plus (`.se.fit` to the power `2`). Add the two together versus using the `sum()` function!
```

```
twins_sigma <- model %>%  # Get model-level information  glance() %>%  # Pull out sigma  pull(sigma)predictions %>%  # Calculate the std err of the predictions  mutate(std_err_of_predictions = sqrt(twins_sigma ^ 2 + .se.fit ^ 2))
```





`critical_value` has already been loaded into your workspace.

- Calculate the prediction interval for the foster twins' IQ as the model prediction, `.fitted` plus or minus the critical value times the standard error of the predictions.


```
# From previous steptwins_sigma <- model %>% glance() %>% pull(sigma)predictions2 <- predictions %>%  mutate(std_err_of_predictions = sqrt(twins_sigma ^ 2 + .se.fit ^ 2))predictions3 <- predictions2 %>%  # Calculate the prediction intervals  mutate(    lower_response_prediction = ___,    upper_response_prediction = ___  )# See the resultpredictions3
```



```
- Calculate `lower_response_prediction` as `.fitted` minus `critical_value` times `std_err_of_predictions`.- For `upper_response_prediction`, swap the minus for a plus.
```

```
# From previous steptwins_sigma <- model %>% glance() %>% pull(sigma)predictions2 <- predictions %>%  mutate(std_err_of_predictions = sqrt(twins_sigma ^ 2 + .se.fit ^ 2))predictions3 <- predictions2 %>%  # Calculate the prediction intervals  mutate(    lower_response_prediction = .fitted - critical_value * std_err_of_predictions,    upper_response_prediction = .fitted + critical_value * std_err_of_predictions  )# See the resultpredictions3
```





Update the plot to add a ribbon layer of prediction intervals.

- Use `Biological` on the x-axis, and make the y-axis go from `lower_response_prediction` (`ymin`) to `upper_response_prediction` (`ymax`).
- Use the `predictions3` dataset.
- Set the transparency level, `alpha`, to `0.2`.
- Use a `fill` color of `"red"`.



```
# From previous step3twins_sigma <- model %>% glance() %>% pull(sigma)predictions3 <- predictions %>%  mutate(    std_err_of_predictions = sqrt(twins_sigma ^ 2 + .se.fit ^ 2),    lower_response_prediction = .fitted - critical_value * std_err_of_predictions,    upper_response_prediction = .fitted + critical_value * std_err_of_predictions  )# Update the plotggplot() +   geom_point(aes(x = Biological, y = Foster), data = twins) +  geom_smooth(aes(x = Biological, y = Foster), data = twins, method = "lm") +   # Add a ribbon layer  ___(    # ... of lower_response_prediction to upper_response_prediction vs. Biological    ___,     # ... using the predictions3 dataset    ___,     # ... with transparency set to 0.2    ___,     # ... and fill color red    ___  )
```


```
- Add `geom_ribbon()`.- Inside `aes()`, map `x` to `Biological`, `ymin` to `lower_response_prediction`, and `ymax` to `upper_response_prediction`.- Set `data` to `predictions3`, `alpha` to `0.2`, and `fill` to `"red"`.
```


```
# From previous step3twins_sigma <- model %>% glance() %>% pull(sigma)predictions3 <- predictions %>%  mutate(    std_err_of_predictions = sqrt(twins_sigma ^ 2 + .se.fit ^ 2),    lower_response_prediction = .fitted - critical_value * std_err_of_predictions,    upper_response_prediction = .fitted + critical_value * std_err_of_predictions  )# Update the plotggplot() +   geom_point(aes(x = Biological, y = Foster), data = twins) +  geom_smooth(aes(x = Biological, y = Foster), data = twins, method = "lm") +   # Add a ribbon layer  geom_ribbon(    # ... of lower_response_prediction to upper_response_prediction vs. Biological    aes(x = Biological, ymin = lower_response_prediction, ymax = upper_response_prediction),     # ... using the predictions3 dataset    data = predictions3,     # ... with transparency set to 0.2    alpha = 0.2,     # ... and fill color red    fill = "red"  )
```









