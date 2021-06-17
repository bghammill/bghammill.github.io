# Lesson 9

In this lesson you’ll learn about using logistic regression, a generalized linear model (GLM), to predict a binary outcome and classify observations.

## What is logistic regression?

Thus far, we have only built models for a numeric response variable.

### A categorical response variable

A well-known Stanford University study on heart transplants tracked the five-year survival rate of patients with dire heart ailments. The purpose of the study was to assess the efficacy of heart transplants, but for right now we will simply focus on modeling the survival rates of these patients. This plot illustrates how those patients who were older when the study began were more likely to be dead when the study ended (five years later).

Note that we have used the `geom_jitter()` function to create the illusion of separation in our data. Because the y value is categorical, all of the points would either lie exactly on “dead” or “alive”, making the individual points hard to see. To counteract this, `geom_jitter()` will move the points a small random amount up or down.

If you fit a regression line to these data, what would it look like?

```
ggplot(data = heart_transplant, aes(x = age, y = survived)) + 
  geom_jitter(width = 0, height = 0.05, alpha = 0.5)
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-age-1.png)



### Making a binary variable

First, we have a technical problem, in that the levels of our response variable are labels, and you can’t build a regression model to a variable that consists of words! We can get around this by creating a new variable that is binary (either 0 or 1), based on whether the patient `survived` to the end of the study. We call this new variable `is_alive`.



### Visualizing a binary response

We can then visualize our `data_space`. The vertical axis can now be thought of as the probability of being alive at the end of the study, given one’s age at the beginning.

```
data_space <- ggplot(data = heart_transplant, aes(x = age, y = is_alive)) + 
  geom_jitter(width = 0, height = 0.05, alpha = 0.5)
data_space
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-data-space-1.png)

### Regression with a binary response

Now there is nothing preventing us from fitting a simple linear regression model to these data, and in fact, in certain cases this may be an appropriate thing to do.

But it’s not hard to see that the line doesn’t fit very well. There are other problems as well…

```
data_space +
  geom_smooth(method = "lm", se = FALSE)
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-lm-1.png)



### Limitations of regression

- Could make nonsensical predictions
- Binary response problematic

What would this model predict as the probability of a 70-year-old patient being alive? It would be a number less than zero, which doesn’t make sense as a probability. Because the regression line always extends to infinity in either direction, it will make predictions that are not between 0 and 1, sometimes even for reasonable values of the explanatory variable.

Second, the variability in a binary response may violate a number of other assumptions that we make when we do inference in multiple regression. You’ll learn about those assumptions in the tutorial on inference for regression.



### Generalized linear models

Thankfully, a modeling framework exists that generalizes regression to include response variables that are non-normally distributed. This family is called **generalized linear models** or GLMs for short. One member of the family of GLMs is called **logistic regression**, and this is the one that models a binary response variable.

A full treatment of GLMs is beyond the scope of this tutorial, but the basic idea is that you apply a so-called link function to appropriately transform the scale of the response variable to match the output of a linear model. The link function used by logistic regression is the **logit** function. This constrains the fitted values of the model to always lie between 0 and 1, as a valid probability must.

In this lesson we will cover:
\- generalization of multiple regression - model non-normal responses - special case: logistic regression - models binary response - uses logitlogit link function - logit(p)=log(p1−p)=β0+β1⋅x



### Fitting a GLM

```
## 
## Call:  glm(formula = is_alive ~ age, family = binomial, data = heart_transplant)
## 
## Coefficients:
## (Intercept)          age  
##     1.56438     -0.05847  
## 
## Degrees of Freedom: 102 Total (i.e. Null);  101 Residual
## Null Deviance:       120.5 
## Residual Deviance: 113.7     AIC: 117.7
## 
## Family: binomial 
## Link function: logit
```

Fitting a GLM in R requires only two small changes from fitting a regression model using `lm()`. First, the function is called `glm()` instead of `lm()`. Second, we have to specify which kind of GLM we want using the family argument.

For logistic regression, we specify the `binomial` family, which uses the logit link function.



### Fitting a line to a binary response

When our response variable is binary, a regression model has several limitations. Among the more obvious - and logically incongruous - is that the regression *line* extends infinitely in either direction. This means that even though our response variable yy only takes on the values 0 and 1, our fitted values y^y^ can range anywhere from −∞−∞ to ∞∞. This doesn’t make sense.

To see this in action, we’ll fit a linear regression model to data about 55 students who applied to medical school. We want to understand how their undergraduate GPAGPA relates to the probability they will be accepted by a particular school (Acceptance)(Acceptance).

The medical school acceptance data is loaded as `MedGPA`.

- Create a scatterplot called `data_space` for `Acceptance` as a function of `GPA`. Use `geom_jitter()` to apply a small amount of jitter to the points in the yy-direction by setting `width = 0` and `height = 0.05`.
- Use `geom_smooth()` to add the simple linear regression line to `data_space`.

### Fitting a line to a binary response (2)

In the previous exercise, we identified a major limitation to fitting a linear regression model when we have a binary response variable. However, it is not *always* inappropriate to do so. Note that our regression line only makes illogical predictions (i.e. y^<0y^<0 or y^>1y^>1) for students with very high or very low GPAs. For GPAs closer to average, the predictions seem fine.

Moreover, the alternative logistic regression model - which we will fit next - is very similar to the linear regression model for observations near the average of the explanatory variable. It just so happens that the logistic curve is very straight near its middle. Thus, in these cases a linear regression model may still be acceptable, even for a binary response.

- Use `filter()` to find the subset of the observations in `MedGPA` whose GPAs are between 3.375 and 3.77, *inclusive*.
- Create a scatterplot called `data_space` for `Acceptance` as a function of `GPA` for only those observations. Use `geom_jitter()` to apply `0.05` jitter to the points in the yy-direction and no jitter to the xx direction.
- Use `geom_smooth()` to add only the simple linear regression line to `data_space`.

### Fitting a model

Logistic regression is a special case of a broader class of generalized linear models, often known as GLMs. Specifying a logistic regression model is very similar to specify a regression model, with two important differences:

- We use the `glm()` function instead of `lm()`
- We specify the `family` argument and set it to `binomial`. This tells the GLM function that we want to fit a logistic regression model to our binary response. The terminology stems from the assumption that our binary response follows a what is called a binomial distribution.

We still use the `formula` and `data` arguments with `glm()`.

Note that the mathematical model is now:

log(y1−y)=β0+β1⋅x+ϵ,log⁡(y1−y)=β0+β1⋅x+ϵ,

where ϵϵ is the error term.



Use `glm()` to fit a logistic regression model for `Acceptance` as a function of `GPA`.



## Visualizing logistic regression

### The data space

```
data_space <- ggplot(data = heart_transplant, aes(x = age, y = is_alive)) + 
  geom_jitter(width = 0, height = 0.05, alpha = 0.5)
data_space
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-data-space-redux-1.png)

Let’s return to our heart transplant data. In the data space, we can see the relationship between age and our binary response variable: whether the patient was alive when the study ended. Here again we’ve added some jitter and transparency to the points to make the individual observations easier to see.



### Regression

When we fit the simple linear regression line previously, we noted how the line was headed towards illogical predictions: the expected probability of a 70-year-old would be less than 0.

```
data_space + 
  geom_smooth(method = "lm", se = FALSE)
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-lm-redux-1.png)



### Using geom_smooth()

```
data_space + 
  geom_smooth(method = "lm", se = FALSE) + 
  geom_smooth(method = "glm", se = FALSE, color = "red", 
              method.args = list(family = "binomial"))
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-glm-1.png)

In contrast, notice how the logistic regression line is curved—most noticeably at the ends. The red logistic regression line will never reach 0 or 1, eliminating those invalid predicted probabilities. In this case, for most ages, the simple linear regression line and the logistic regression line don’t differ by very much, and you might not lose much by using the simpler regression model. But for older people, the logistic model should perform much better.

How can we visually assess how well the logistic model fits the data? Since the actual observations are all at 0 or 1, but our predictions are always between 0 and 1, we will always be off by some amount. In particular, our model predicts a 50% chance of survival for patients that are about 27 years old. Is that accurate?

### Using bins

```
heart_breaks <- heart_transplant %>%
  pull(age) %>%
  quantile(probs = 0:7/7)

data_binned_space <- data_space + 
  stat_summary_bin(
    fun = "mean", color = "red", 
    geom = "line", breaks = heart_breaks
  )

data_binned_space
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-data-binned-1.png)

One way to address this question is to separate the observations into bins based on age, and then compute the average probability of being alive for each age group. Here, we separate the data into seven bins such that each bin contains roughly the same number of observations. The choice of how to define the bins is somewhat arbitrary, but this choice seems to provide us with a reasonable picture of what is happening. In general, it seems clear that the probability of being alive declines with age.



### Adding the model to the binned plot

```
mod_heart <- glm(is_alive ~ age, data = heart_transplant, family = binomial)

data_binned_space + 
  geom_line(
    data = augment(mod_heart, type.predict = "response"), 
    aes(y = .fitted), color = "blue"
  )
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-data-binned-line-1.png)

To add our model to the plot, we’ll employ the same technique that we used for the parallel slopes models. First, we use the `augment()` function from the broom package to compute the fitted values for our original observations based on our model. Note that we have set the `type.predict` argument to ensure that the fitted values are on the same scale as the response variable. Second, we use the `geom_line()` function to draw a blue line through these points.

This blue line is the same as the one we drew previously using `geom_smooth()`. With the binned observations in red, we can now see how the blue logistic regression line fits “through” these binned points.

Continue

### Using `geom_smooth()`

Our logistic regression model can be visualized in the data space by overlaying the appropriate logistic curve. We can use the `geom_smooth()` function to do this. Recall that `geom_smooth()` takes a `method` argument that allows you to specify what type of smoother you want to see. In our case, we need to specify that we want to use the `glm()` function to do the smoothing.

However we also need to tell the `glm()` function which member of the GLM family we want to use. To do this, we will pass the `family` argument to `glm()` as a list using the `method.args` argument to `geom_smooth()`. This mechanism is common in R, and allows one function to pass a list of arguments to another function.

- Create a scatterplot called `data_space` for `Acceptance` as a function of `GPA`. Use `geom_jitter()` to apply a small amount of jitter to the points in the yy-direction. Set `width = 0` and `height = 0.05` in `geom_jitter()`.
- Use `geom_smooth()` to add the logistic regression line to `data_space` by specifying the `method` and `method.args` arguments to fit a logistic `glm`.



### Using bins

One of the difficulties in working with a binary response variable is understanding how it “changes.” The response itself (yy) is *either* 0 or 1, while the fitted values (y^y^) - which are interpreted as probabilities - are *between* 0 and 1. But if every medical school applicant is either admitted or not, what does it mean to talk about the *probability* of being accepted?

What we’d like is a larger sample of students, so that for each GPA value (e.g. 3.54) we had many observations (say nn), and we could then take the average of those nn observations to arrive at the estimated probability of acceptance. Unfortunately, since the explanatory variable is continuous, this is hopeless - it would take an infinite amount of data to make these estimates robust.

Instead, what we can do is put observations into *bins* based on their GPA value. Within each bin, we can compute the proportion of accepted students, and we can visualize our model as a smooth logistic curve through those binned values.

We have created a `data.frame` called `MedGPA_binned` that aggregates the original data into separate bins for each 0.25 of GPA. It also contains the fitted values from the logistic regression model.

Here we are plotting yy as a function of xx, where that function is



y=exp(β^0+β^1⋅x)1+exp(β^0+β^1⋅x).y=exp⁡(β^0+β^1⋅x)1+exp⁡(β^0+β^1⋅x).



Note that the left hand side is the expected probability yy of being accepted to medical school.

- Create a scatterplot called `data_space` for `acceptance_rate` as a function of `mean_GPA` using the binned data in `MedGPA_binned`. Use `geom_line()` to connect the points.
- Augment the model `mod`. Create predictions on the scale of the response variable by using the `type.predict` argument.
- Use `geom_line()` to illustrate the model through the fitted values.



## Three scales approach to interpretation

### Probability scale

For the Stanford heart transplant patients, we’ve observed how the probability of survival seems to decline with age. The notion of probability here is very intuitive: it’s easy to understand what we mean when we say that the five-year survival rate is 75%.



y^=exp(β^0+β^1⋅x)1+exp(β^0+β^1⋅x)y^=exp⁡(β^0+β^1⋅x)1+exp⁡(β^0+β^1⋅x)



Here, we compute the fitted probabilities using the `augment()` function.



### Probability scale plot

Unfortunately, since our model is now non-linear, it’s harder to succinctly characterize how those probabilities decline.

```
ggplot(heart_transplant_plus, aes(x = age, y = y_hat)) + 
  geom_point() + 
  geom_line() + 
  scale_y_continuous("Probability of being alive", limits = c(0, 1))
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-glm-prob-1.png)

We can no longer say that “each additional year of age is associated with a particular change in the probability of surviving,” because that change in probability is not constant across ages. Thus, while the probability scale is natural, it can be cumbersome to work with.



### Odds scale

To combat the problem of the scale of the y variable, we can change the scale of the variable on the y-axis. Instead of thinking about the probability of survival, we can think about the odds. While these two concepts are often conflated, they are not the same. They are however, related by the simple formula below. The **odds** of a binary event are the ratio of how often it happens, to how often it doesn’t happen.



odds(y^)=y^1−y^=exp(β^0+β^1⋅x)odds(y^)=y^1−y^=exp⁡(β^0+β^1⋅x)



Thus, if the probability of survival is 75%, then the odds of survival are 3:1, since you are three times more likely to survive than you are to die. Odds are commonly used to express uncertainty in a variety of contexts, most notably gambling.

```
heart_transplant_plus <- heart_transplant_plus %>%
  mutate(odds_hat = y_hat / (1 - y_hat))
```

### Odds scale plot

```
ggplot(heart_transplant_plus, aes(x = age, y = odds_hat)) + 
  geom_point() + 
  geom_line() + 
  scale_y_continuous("Odds of being alive")
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-glm-odds-1.png)

If we change the y-scale to odds, then our model must change shape as well. In fact, our model now has the form of an exponential function. In this case, the odds of survival decrease exponentially as people age.



### Log-odds scale



logit(y^)=log[y^1−y^]=β^0+β^1⋅xlogit(y^)=log⁡[y^1−y^]=β^0+β^1⋅x



```
heart_transplant_plus <- heart_transplant_plus %>%
  mutate(log_odds_hat = log(odds_hat))
```

While the odds scale is more useful than the probability scale for certain things, it isn’t entirely satisfying. Statisticians also think about logistic regression models on the log-odds scale, which is formed by taking the **natural log** of the odds.



### Log-odds plot

```
ggplot(heart_transplant_plus, aes(x = age, y = log_odds_hat)) + 
  geom_point() + 
  geom_line() + 
  scale_y_continuous("Log(odds) of being alive")
```

![img](https://openintro.shinyapps.io/ims-04-multivariable-and-logistic-models-04/_w_7d891fa1/04-04-lesson_files/figure-html/heart-glm-log-odds-1.png)

The benefit to this approach is clear: now the logistic regression model can be visualized as a line!

Unfortunately, understanding what the log of the odds of an event means is very difficult for humans.

### Comparison

- Probability scale
  - scale: intuitive, easy to interpret
  - function: non-linear, hard to interpret
- Odds scale
  - scale: harder to interpret
  - function: exponential, harder to interpret
- Log-odds scale
  - scale: impossible to interpret
  - function: linear, easy to interpret

So we’ve identified three different scales when working with logistic regression models. Each has its own strengths but also weaknesses, and so you really can’t stick with one scale and ignore the others.

The probability scale is the easiest to understand, but it makes the logistic function difficult to interpret. Conversely the logistic function becomes a line on the log-odds scale. This makes the function easy to interpret, but the log of the odds is hard to grapple with. The odds scale lies somewhere in between.

### Odds ratios



OR=odds(y^|x+1)odds(y^|x)=exp(β^0+β^1⋅(x+1))exp(β^0+β^1⋅x)=expβ1OR=odds(y^|x+1)odds(y^|x)=exp⁡(β^0+β^1⋅(x+1))exp⁡(β^0+β^1⋅x)=exp⁡β1



```
## (Intercept)         age 
##   4.7797050   0.9432099
```

Moreover, it is the odds scale that leads to the most common interpretation of the coefficients in a logistic regression model. As noted previously, interpreting the coefficients on the probability scale is hard because the model is non-linear, while interpreting them on the log-odds scale is hard because the scale is abstruse. However, on the odds scale we can form the ratio of the odds when the explanatory variable increases by one unit. This works out mathematically to be equal to the exponential of β1β1, or eβ1eβ1, the “slope” coefficient.

Our interest is in how this number differs from 1. If it’s greater than one, then the odds increase. Conversely, if it’s less than one, then the odds decrease. In our case, our model suggests that each additional year of age is associated with a 6% decrease in the odds of survival.

Keeping careful track of which scale you are working on will help you get these interpretations right.

Continue

### Odds scale

For most people, the idea that we could estimate the probability of being admitted to medical school based on undergraduate GPA is fairly intuitive. However, thinking about how the probability changes as a function of GPA is complicated by the non-linear logistic curve. By translating the response from the probability scale to the [odds](https://en.wikipedia.org/wiki/Odds) scale, we make the right hand side of our equation easier to understand.

If the probability of getting accepted is yy, then the odds are y/(1−y)y/(1−y). Expressions of probabilities in terms of odds are common in many situations, perhaps most notably gambling.

Here we are plotting y/(1−y)y/(1−y) as a function of xx, where that function is



odds(y^)=y^1−y^=exp(β^0+β^1⋅x)odds(y^)=y^1−y^=exp⁡(β^0+β^1⋅x)



Note that the left hand side is the expected *odds* of being accepted to medical school. The right hand side is now a familiar exponential function of xx.

Already loaded for you are two data frames: the `MedGPA_binned` data frame contains the data for each GPA bin, while the `MedGPA_plus` data frame records the original observations after being `augment()`-ed by `mod`.

- Add a variable called `odds` to `MedGPA_binned` that records the odds of being accepted to medical school for each bin.
- Create a scatterplot called `data_space` for `odds` as a function of `mean_GPA` using the binned data in `MedGPA_binned`. Connect the points with `geom_line()`.
- Add a variable called `odds_hat` to `MedGPA_plus` that records the predicted odds of being accepted for each observation.
- Use `geom_line()` to illustrate the model through the fitted values. Note that you should be plotting the oddsˆodds^’s.

### Log-odds scale

Previously, we considered two formulations of logistic regression models:

- on the probability scale, the units are easy to interpret, but the function is non-linear, which makes it hard to understand
- on the odds scale, the units are harder (but not impossible) to interpret, and the function in exponential, which makes it harder (but not impossible) to interpret

We’ll now add a third formulation:

- on the log-odds scale, the units are nearly impossible to interpret, but the function is linear, which makes it easy to understand

As you can see, none of these three is uniformly superior. Most people tend to interpret the fitted values on the probability scale and the function on the log-odds scale. The interpretation of the coefficients is most commonly done on the odds scale. Recall that we interpreted our slope coefficient β1β1 in *linear* regression as the expected change in yy given a one unit change in xx. On the probability scale, the function is non-linear and so this approach won’t work. On the log-odds scale, the function is linear, but the units are not interpretable (it is difficult to answer the question: what does the loglog of the odds mean??). However, on the odds scale, a one unit change in xx leads to the odds being multiplied by a factor of β1β1. To see why, we form the **odds ratio**:



OR=odds(y^|x+1)odds(y^|x)=expβ1OR=odds(y^|x+1)odds(y^|x)=exp⁡β1



Thus, the exponentiated coefficient β1β1 tells us how the expected *odds* change for a one unit increase in the explanatory variable. It is tempting to interpret this as a change in the expected *probability*, but this is wrong and can lead to nonsensical predictions (e.g. expected probabilities greater than 1).

- Add a variable called `log_odds` to `MedGPA_binned` that records the odds of being accepted for each bin. Recall that odds(p)=p/(1−p)odds(p)=p/(1−p).
- Create a scatterplot called `data_space` for `log_odds` as a function of `mean_GPA` using the binned data in `MedGPA_binned`. Use `geom_line` to connect the points.
- Add a variable called `log_odds_hat` to `MedGPA_plus` that records the predicted odds of being accepted for each observation.
- Use `geom_line()` to illustrate the model through the fitted values. Note that you should be plotting the logoddsˆlog⁡odds^’s.

### Interpretation of logistic regression

The fitted coefficient β^1β^1 from the medical school logistic regression model is 5.45. The exponential of this is 233.73.

Donald’s GPA is 2.9, and thus the model predicts that the probability of him getting into medical school is 3.26%. The odds of Donald getting into medical school are 0.0337, or - phrased in gambling terms - 29.6:1.

**Food for thought:** If Donald hacks the school’s registrar and changes his GPA to 3.9, how would his expected odds of getting into medical school change?

## Using a logistic model

### Learning from a model

```
mod <- glm(is_alive ~ age + transplant, 
                    data = heart_transplant, family = binomial)

exp(coef(mod))
##         (Intercept)                 age transplanttreatment 
##           2.6461676           0.9265153           6.1914009
```

One important reason to build a model is to learn from the coefficients about the underlying random process. For example, in the Stanford heart transplant study, we were able to estimate the effect of age on the five-year survival rate. This simple model shed no light on the obvious purpose of the study, which was to determine whether those patients who received heart transplants were likely to live longer than the control group that received no transplant.

By including the transplant variable in our model and exponentiating the coefficients, we see a huge effect. Patients who received a heart transplant saw their odds of survival improve by a factor of 6.2, even after controlling for age. Note that as expected, age still has a deleterious effect on mortality.



### Using augment()

```
# log-odds scale
augment(mod)
```



| is_alive<dbl> | age<int> | transplant<fct> | .fitted<dbl> | .resid<dbl> | .std.resid<dbl> |  .hat<dbl> |      |
| ------------: | -------: | :-------------- | -----------: | ----------: | --------------: | ---------: | ---- |
|             0 |       53 | control         |   -3.0720949 |  -0.3009421 |      -0.3042949 | 0.02191525 |      |
|             0 |       43 | control         |   -2.3088482 |  -0.4352986 |      -0.4418715 | 0.02952903 |      |
|             0 |       52 | control         |   -2.9957702 |  -0.3123727 |      -0.3159477 | 0.02250241 |      |
|             0 |       52 | control         |   -2.9957702 |  -0.3123727 |      -0.3159477 | 0.02250241 |      |
|             0 |       54 | control         |   -3.1484196 |  -0.2899116 |      -0.2930564 | 0.02134668 |      |
|             0 |       36 | control         |   -1.7745756 |  -0.5596850 |      -0.5713271 | 0.04033929 |      |
|             0 |       47 | control         |   -2.6141469 |  -0.3759601 |      -0.3809213 | 0.02587839 |      |
|             0 |       41 | treatment       |   -0.3330375 |  -1.0396433 |      -1.0497763 | 0.01921191 |      |
|             0 |       47 | control         |   -2.6141469 |  -0.3759601 |      -0.3809213 | 0.02587839 |      |
|             0 |       51 | control         |   -2.9194456 |  -0.3242157 |      -0.3280286 | 0.02311200 |      |

Next

123456

...

11

Previous

1-10 of 103 rows | 1-7 of 9 columns

As we have seen, running the augment() function on the model object will return a data frame with—among other things—the fitted values. However, when we run this with the default options, the fitted values sure don’t look like probabilities! These are the fitted values on the log-odds scale, which aren’t terribly useful to us.



### Making probabilistic predictions

```
# probability scale
augment(mod, type.predict = "response")
```



| is_alive<dbl> | age<int> | transplant<fct> | .fitted<dbl> | .resid<dbl> | .std.resid<dbl> |  .hat<dbl> |      |
| ------------: | -------: | :-------------- | -----------: | ----------: | --------------: | ---------: | ---- |
|             0 |       53 | control         |   0.04427310 |  -0.3009421 |      -0.3042949 | 0.02191525 |      |
|             0 |       43 | control         |   0.09039280 |  -0.4352986 |      -0.4418715 | 0.02952903 |      |
|             0 |       52 | control         |   0.04761733 |  -0.3123727 |      -0.3159477 | 0.02250241 |      |
|             0 |       52 | control         |   0.04761733 |  -0.3123727 |      -0.3159477 | 0.02250241 |      |
|             0 |       54 | control         |   0.04115360 |  -0.2899116 |      -0.2930564 | 0.02134668 |      |
|             0 |       36 | control         |   0.14497423 |  -0.5596850 |      -0.5713271 | 0.04033929 |      |
|             0 |       47 | control         |   0.06823348 |  -0.3759601 |      -0.3809213 | 0.02587839 |      |
|             0 |       41 | treatment       |   0.41750173 |  -1.0396433 |      -1.0497763 | 0.01921191 |      |
|             0 |       47 | control         |   0.06823348 |  -0.3759601 |      -0.3809213 | 0.02587839 |      |
|             0 |       51 | control         |   0.05120063 |  -0.3242157 |      -0.3280286 | 0.02311200 |      |

Next

123456

...

11

Previous

1-10 of 103 rows | 1-7 of 9 columns

However, if we set the `type.predict` argument to “response”, we retrieve the fitted values on the familiar probability scale.

Making predictions about the probability of survival for those patients who took part in the study is of somewhat limited value. We already know whether they survived! Aside from learning about the efficacy of the treatment, another common purpose for modeling is to make predictions for observations that are not part of our data set. These are called **out-of-sample** predictions.

For example, former Vice President Dick Cheney famously received a heart transplant in March of 2012 at the age of 71. More than five years later, Cheney is still alive, but what does our model predict for his five-year survival rate?



### Out-of-sample predictions

```
cheney <- data.frame(age = 71, transplant = "treatment")

augment(mod, newdata = cheney, type.predict = "response")
```



| age<dbl> | transplant<chr> | .fitted<dbl> |
| -------: | :-------------- | -----------: |
|       71 | treatment       |   0.06768681 |

1 row

To compute this, we build a data frame with Cheney’s data, and run it through our model using the newdata argument to `augment()`. The results suggest that Cheney had only a 6.8% chance of survival. Either Cheney is quite lucky to be alive, or—more likely—the survival rates of all heart transplant patients have improved considerably since the Stanford study was completed in 1973.



### Making binary predictions

```
mod_plus <- augment(mod, type.predict = "response") %>%
  mutate(alive_hat = round(.fitted))
  
mod_plus %>%
  select(is_alive, age, transplant, .fitted, alive_hat)
```



| is_alive<dbl> | age<int> | transplant<fct> | .fitted<dbl> | alive_hat<dbl> |
| ------------: | -------: | :-------------- | -----------: | -------------: |
|             0 |       53 | control         |   0.04427310 |              0 |
|             0 |       43 | control         |   0.09039280 |              0 |
|             0 |       52 | control         |   0.04761733 |              0 |
|             0 |       52 | control         |   0.04761733 |              0 |
|             0 |       54 | control         |   0.04115360 |              0 |
|             0 |       36 | control         |   0.14497423 |              0 |
|             0 |       47 | control         |   0.06823348 |              0 |
|             0 |       41 | treatment       |   0.41750173 |              0 |
|             0 |       47 | control         |   0.06823348 |              0 |
|             0 |       51 | control         |   0.05120063 |              0 |

Next

123456

...

11

Previous

1-10 of 103 rows

If our response variable is binary, then why are we making probabilistic predictions? Shouldn’t we be able to make binary predictions? That is, instead of predicting the probability that a person survives for five years, shouldn’t we be able to predict definitively whether they will live or die?

There are a number of different ways in which we could reasonably convert our probabilistic fitted values into a binary decision. The simplest way would be to simply round the probabilities.



### Confusion matrix

```
mod_plus %>%
  select(is_alive, alive_hat) %>%
  table()
##         alive_hat
## is_alive  0  1
##        0 71  4
##        1 20  8
```

So how well did our model perform? One common way of assessing performance of models for a categorical response is via a confusion matrix. This simply cross-tabulates the reality with what our model predicted. In this case, our model predicted that 91 patients would die, and only 12 would live. Of those 91, 71 actually did die, while of the 12, 8 actually lived. Thus, our overall accuracy was 79 out of 103, or about 77%.

Note that our model predicted only 12 patients would live, but more than twice as many patients actually survived. Our model’s under-prediction is probably a consequence of the low survival rate overall, coupled with our clumsy rounding scheme. One way to improve the accuracy of our binary predictions would be to experiment with looser rounding thresholds.

### Making probabilistic predictions

Just as we did with linear regression, we can use our logistic regression model to make predictions about new observations. In this exercise, we will use the `newdata` argument to the `augment()` function from the **broom** package to make predictions about students who were not in our original data set. These predictions are sometimes called *out-of-sample*.

Following our previous discussion about scales, with logistic regression it is important that we specify on which scale we want the predicted values. Although the default is `terms` – which uses the log-odds scale – we want our predictions on the probability scale, which is the scale of the `response` variable. The `type.predict` argument to `augment()` controls this behaviour.

A logistic regression model object, `mod`, has been defined for you.

- Create a new data frame which has one variable called `GPA` and one row, with the value 3.51.
- Use `augment()` to find the expected probability of admission to medical school for a student with a GPA of 3.51.

### Making binary predictions

Naturally, we want to know how well our model works. Did it predict acceptance for the students who were actually accepted to medical school? Did it predict rejections for the student who were not admitted? These types of predictions are called *in-sample*. One common way to evaluate models with a binary response is with a confusion matrix. [Yes, that is actually what it is called!]

However, note that while our response variable is binary, our fitted values are probabilities. Thus, we have to round them somehow into binary predictions. While the probabilities convey more information, we might ultimately have to make a decision, and so this rounding is common in practice. There are many different ways to round, but for simplicity we will predict admission if the fitted probability is greater than 0.5, and rejection otherwise.

First, we’ll use `augment()` to make the predictions, and then `mutate()` and `round()` to convert these probabilities into binary decisions. Then we will form the confusion matrix using the `table()` function. `table()` will compute a 2-way table when given a data frame with two categorical variables, so we will first use `select()` to grab only those variables.

You will find that this model made only 15 mistakes on these 55 observations, so it is nearly 73% accurate.

The model object `mod` is already loaded for you.

- Create a data frame with the actual observations, and their fitted probabilities, and add a new column, `Acceptance_hat`, with the *binary* decision by rounding the fitted probabilities.
- Compute the confusion matrix between the actual and predicted acceptance.

You have successfully completed Lesson 4 in Tutorial 4: Multiple and Logistic Regression.

























What’s next?

📒 [Full list of tutorials supporting OpenIntro::Introduction to Modern Statistics](https://openintrostat.github.io/ims-tutorials/)

🗒 [Tutorial 3: Introduction to Linear Models Data](https://openintrostat.github.io/ims-tutorials/03-introduction-to-linear-models/)

1️⃣ [Tutorial 3 - Lesson 1: Visualizing two variables](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-01/)

2️⃣ [Tutorial 3 - Lesson 2: Correlation](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-02/)

3️⃣ [Tutorial 3 - Lesson 3: Simple linear regression](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-03/)

4️⃣ [Tutorial 3 - Lesson 4: Interpreting regression models](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-04/)

5️⃣ [Tutorial 3 - Lesson 5: Model fit](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-05/)

📖 [Learn more at Introduction to Modern Statistics](http://openintro-ims.netlify.app/)





[Next Topic](https://bghammill.github.io/ims-03-model/ims-03-lesson-02/)

[Back to lesson list](https://bghammill.github.io/ims-03-model/)

[Back to tutorial list (main page)](https://bghammill.github.io/)



<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

