# Lesson 7

## Evaluating and extending parallel slopes model

This lesson covers model evaluation. By looking at different properties of the model, including the adjusted R2R2., you’ll learn to compare models so that you can select the best one. You’ll also learn about interaction terms in linear models.

## Model fit, residuals, and prediction

### Residuals

![img](https://openintro.shinyapps.io/ims-03-model-07/_w_024758d8/03-07-lesson_files/figure-html/mpg-displ-resid-1.png)

One of the most natural questions we might have about our model is: “how well does it fit?” We measure this by computing—for each observation—the difference between the actual value of the response variable and the fitted value from our model. This distance is called a **residual**. Residuals for two observations are illustrated in this plot with vertical arrows. Just as in simple linear regression, the model fitting procedure will automatically minimize the length of those arrows across all of the points.

In a parallel slopes model, our model is represented by two lines, but only one of the lines is relevant for each observation. In the plot, the green points—each corresponding to a car made in 2008—are compared to the green line, and the orange points—each corresponding to a car made in 1999—are compared to the orange line.

Note that this necessarily makes our parallel slopes model more flexible than a simple linear regression model. The two lines are constrained to be parallel, but we still have two instead of one. If the best fit occurs when the two lines are very close together, then the coefficient of the categorical variable will be very small, and the parallel slopes model will be very similar to the simple linear regression model.

### Model Fit

The coefficient of determination—usually called R-squared or R2R2—carries over from simple linear regression. Recall that the sum of the squared residuals (or errors) is denoted SSE. If the model fits the data better, then the residuals are smaller, the SSE is smaller, and the R2R2. value is higher. The total sum of the squares—denoted SST—is a function of the response variable alone and does not depend on the model.

In general, a higher R2R2. may be a sign of a better model fit, but the situation becomes more complicated in multiple regression because additional explanatory variables will always increase R2R2.. Thus, model fits in multiple regression are often compared using the adjusted R2R2. value defined here. Note that the only difference is that a penalty is applied as the number of explanatory variables p increases. Unlike R2R2., adjusted R2R2. will not necessarily increase as new explanatory variables are added—it could go up or down.

To summarize:

- R2=1−SSESSTR2=1−SSESST
- SSESSE gets smaller as ⇒R2⇒R2 increases
- As pp (number of explanatory variables) increases R2R2 will always increase (or stay the same)
- To adjust for an increasing R2R2 use: $ R^2_{adj} = 1 - $



### Fitted values

```
# returns a vector
predict(mod)
# returns a data.frame
augment(mod)
```

Retrieving the fitted values produced by our model can be done in two different ways. The `predict()` function will return the fitted values as a vector, while the `augment()` function from the broom package will return a data.frame that contains the original observations, the fitted values, the residuals, and several other diagnostic computations.

Since this tutorial uses the tidyverse, we will generally prefer the latter method.

Another important bit of functionality is the ability to make out-of-sample predictions. This means using our model to make predictions about observations that were not part of the data set on which the model was fit.



### 2008 Toyota Matrix

![img](https://openintro.shinyapps.io/ims-03-model-07/_w_024758d8/images/2006_toyota_matrix-pic-49683-1600x1200.jpeg)

For example, suppose you currently drive a 2008 Toyota Matrix like the one you see here. However, this car isn’t in our dataset. What does our model predict for the fuel economy of this car?

### Predictions

```
new_obs <- data.frame(displ = 1.8, year = 2008)
# returns a vector
predict(mod, newdata = new_obs)
##        1 
## 30.17807
# returns a data.frame
augment(mod, newdata = new_obs)
```



| displ<dbl> | year<dbl> | .fitted<dbl> |
| ---------: | --------: | -----------: |
|        1.8 |      2008 |     30.17807 |

1 row

To figure this out, we can simply input the values of the explanatory variables. In this case, my car has a 1.8 liter engine. We can create a new data.frame with the information, and then feed it to `predict()` or `augment()` using the newdata argument. Either function will return a predicted mileage of 30.18 mpg. This is slightly less than the manufacturer’s reported 33 mpg, giving us a residual of 2.82 mpg.

### R2R2. vs. adjusted R2R2.

Two common measures of how well a model fits to data are R2R2 (the coefficient of determination) and the adjusted R2R2. The former measures the percentage of the variability in the response variable that is explained by the model. To compute this, we define



R2=1−SSESST,R2=1−SSESST,



where SSESSE and SSTSST are the sum of the squared residuals, and the total sum of the squares, respectively. One issue with this measure is that the SSESSE can only decrease as new variable are added to the model, while the SSTSST depends only on the response variable and therefore is not affected by changes to the model. This means that you can increase R2R2 by adding *any* additional variable to your model - even random noise.

The adjusted R2R2 includes a term that penalizes a model for each additional explanatory variable (where pp is the number of explanatory variables).



R2adj=1−SSESST⋅n−1n−p−1,Radj2=1−SSESST⋅n−1n−p−1,



We can see both measures in the output of the `summary()` function on our model object.

- Use `summary()` to compute R2R2 and adjusted R2R2 on the model object called `mod`.
- Use `mutate()` and `rnorm()` to add a new variable called `noise` to the `mariokart` data set that consists of random noise. Save the new dataframe as `mariokart_noisy`.
- Use `lm()` to fit a model that includes `wheels`, `cond`, and the random noise term.
- Use `summary()` to compute R2R2 and adjusted R2R2 on the new model object. Did the value of R2R2 increase? What about adjusted R2R2?
- The model `mod` is already in your workspace.
- Add a variable called `noise` by calling `rnorm()` with the argument `n` equal to the `nrow()` of your dataframe.
- The first argument of `lm()` is a formula involving the variables `total_pr`, `wheels`, `cond`, and `noise`.
- Call the function `summary()` with the argument `mod2`.

### Prediction

Once we have fit a regression model, we can use it to make predictions for unseen observations or retrieve the fitted values. Here, we explore two methods for doing the latter.

A traditional way to return the fitted values (i.e. the y^y^’s) is to run the `predict()` function on the model object. This will return a vector of the fitted values. Note that `predict()` will take an optional `newdata` argument that will allow you to make predictions for observations that are not in the original data.

A newer alternative is the `augment()` function from the **broom** package, which returns a `data.frame` with the response variable (yy), the relevant explanatory variables (the xx’s), the fitted value (y^y^) and some information about the residuals (ee). `augment()` will also take a `newdata` argument that allows you to make predictions.

The fitted model `mod` is already in your environment.

- Compute the fitted values of the model as a vector using `predict()`.
- Compute the fitted values of the model as one column in a `data.frame` using `augment()`.

## Understanding Interaction

### Interaction

```
ggplot(data = mpg, aes(x = displ, y = hwy, color = factor(year))) +
  geom_point() + 
  geom_smooth(method = "lm", se = 0)
```

![img](https://openintro.shinyapps.io/ims-03-model-07/_w_024758d8/03-07-lesson_files/figure-html/mpg-interaction-1.png)

Thus far we have considered models where the regression lines were constrained to be parallel. But what if the lines didn’t have to be parallel? In this plot, we illustrate the model when the lines are allowed to have their own slopes. Now, the relationship between fuel economy and engine size is not the same for the newer cars as it is for the older cars. The slope of that relationship now changes based on the year. Thus, engine size and year are allowed to *interact* in their relationship with fuel economy.

### Adding interaction terms



mpg^=β^0+β^1⋅displ+β^2⋅isnewer+β^3⋅displ⋅isnewermpg^=β^0+β^1⋅displ+β^2⋅isnewer+β^3⋅displ⋅isnewer



- For older cars



mpg^=β^0+β^1⋅displmpg^=β^0+β^1⋅displ



- For newer cars,



mpg^=(β^0+β^2)+(β^1+β^3)⋅displmpg^=(β^0+β^2)+(β^1+β^3)⋅displ



Mathematically, we achieve this by adding yet another term to our model. This third explanatory variable is the product of `displ` and `is_newer`, and it is called an **interaction** term.

The addition of this term results in the two regression lines shown above. Note that unlike in the previous model, now both the intercepts and the slopes are different.

### Interaction syntax

```
# add interaction term manually
lm(hwy ~ displ + factor(year) + displ:factor(year), data = mpg)
## 
## Call:
## lm(formula = hwy ~ displ + factor(year) + displ:factor(year), 
##     data = mpg)
## 
## Coefficients:
##            (Intercept)                   displ        factor(year)2008  
##                35.7922                 -3.7684                  0.3445  
## displ:factor(year)2008  
##                 0.3052
```

In R, we can add an interaction term using some new syntax. Here, we simply add a third term to our model, using the colon to denote multiplication of the explanatory variables in the interaction term. This produces a model with four coefficients:

- one for the intercept,
- one for displacement,
- one for the year, and
- one for the interaction of displacement with year.

### Reasoning about interaction

Here is the no interaction (parallel slopes) model.

```
lm(hwy ~ displ + factor(year), data = mpg)
## 
## Call:
## lm(formula = hwy ~ displ + factor(year), data = mpg)
## 
## Coefficients:
##      (Intercept)             displ  factor(year)2008  
##           35.276            -3.611             1.402
```

And here is the interaction model.

```
lm(hwy ~ displ + factor(year) + displ:factor(year), data = mpg)
## 
## Call:
## lm(formula = hwy ~ displ + factor(year) + displ:factor(year), 
##     data = mpg)
## 
## Coefficients:
##            (Intercept)                   displ        factor(year)2008  
##                35.7922                 -3.7684                  0.3445  
## displ:factor(year)2008  
##                 0.3052
```

Interpreting the coefficients in an interaction model becomes more complicated. Note the difference between the fitted coefficients of the parallel slopes model with the interaction model. The original slope of -3.61 mpg per liter for all cars is now separated into two slopes: -3.77 mpg per liter for older cars, and -3.46 mpg per liter for newer cars. Thus, fuel economy for the older cars is not only lower overall, but it also declines more rapidly as a function of engine size. This importantly changes the interpretation of the model. It suggests that the greater fuel economy of the 2008 cars is not just related to the fact that they had smaller engines, on average – a function mainly of consumer choice. It suggests that the 2008 cars were also engineered better, in that they were able to maintain fuel economy slightly better even with larger engine sizes.

Now, the size of that difference is small, and as the plot suggests, it doesn’t appear to make a practical difference. In this case, the difference is not statistically significant, and thus the parallel slopes model is probably a better representation of the data, but you will tackle that subtlety in another tutorial.



### Thought experiments

Suppose that after going apple picking you have 12 apples left over. You decide to conduct an experiment to investigate how quickly they will rot under certain conditions. You place six apples in a cool spot in your basement, and leave the other six on the window sill in the kitchen. Every week, you estimate the percentage of the surface area of the apple that is rotten or moldy.

Consider the following models:



rot=β0+β1⋅t+β2⋅temp,rot=β0+β1⋅t+β2⋅temp,



and



rot=β0+β1⋅t+β2⋅temp+β3⋅temp⋅t,rot=β0+β1⋅t+β2⋅temp+β3⋅temp⋅t,



where tt is time, measured in weeks, and temptemp is a binary variable indicating either cool or warm.

If you decide to keep the interaction term present in the second model, you are implicitly assuming that:

*Hint:* An interaction term allows the two lines representing the amount of rot in each location to have different slopes.

If you decide to keep the interaction term present in the second model, you are implicitly assuming that

The amount of rot will vary based on the temperature.

The amount of rot will vary based on the temperature, after controlling for the length of time they have been left out.

The *rate* at which apples rot will vary based on the temperature.

Time and temperature are independent.



### Fitting a model with interaction

Including an interaction term in a model is easy - we just have to tell `lm()` that we want to include that new variable. An expression of the form

```
lm(y ~ x + z + x:z, data = mydata)
```

will do the trick. The use of the colon (`:`) here means that the interaction between xx and zz will be a third term in the model.

The data frame `mariokart` is already loaded in your workspace.

- Use `lm()` to fit a model for the price of a MarioKart as a function of its condition and the duration of the auction, *with interaction*.

Include the term `cond:duration` in your formula to model the interaction of condition and duration.



### Visualizing interaction models

Interaction allows the slope of the regression line in each group to vary. In this case, this means that the relationship between the final price and the length of the auction is moderated by the condition of each item.

Interaction models are easy to visualize in the data space with **ggplot2** because they have the same coefficients as if the models were fit independently to each group defined by the level of the categorical variable. In this case, new and used MarioKarts each get their own regression line. To see this, we can set an aesthetic (e.g. `color`) to the categorical variable, and then add a `geom_smooth()` layer to overlay the regression line for each color.

The dataset `mariokart` is already loaded in your workspace.

- Use the `color` aesthetic and the `geom_smooth()` function to plot the interaction model between duration and condition in the data space. Make sure you set the `method` and `se` arguments of `geom_smooth()`.



## Simpson’s Paradox

### SAT scores and teacher salary

It seems reasonable to think that by paying their teachers a higher salary, schools could attract better teachers, which would lead to better student outcomes. Yet if we fit a simple linear regression model for the average SAT score among students as a function of average teacher salary across all US states, we see a negative slope. This suggests that states that pay higher teacher salaries—on average—are associated with lower student performance on the SAT. What gives?

```
ggplot(data = SAT, aes(x = salary, y = total)) + 
  geom_point() + 
  geom_smooth(method = "lm", se = 0)
```

![img](https://openintro.shinyapps.io/ims-03-model-07/_w_024758d8/03-07-lesson_files/figure-html/sat-salary-1.png)



### Percentage taking the SAT

Let’s try turning the SAT score into an ordinal variable with 3 levels.

```
SAT <- SAT %>%
  mutate(sat_bin = cut(frac, 3))
mod <- lm(formula = total ~ salary + sat_bin, data = SAT)

tidy(mod)
```



| term<chr>          | estimate<dbl> | std.error<dbl> | statistic<dbl> | p.value<dbl> |
| :----------------- | ------------: | -------------: | -------------: | -----------: |
| (Intercept)        |  1000.7173444 |      33.547996 |     29.8294224 | 9.659605e-32 |
| salary             |     0.8697269 |       1.034042 |      0.8410943 | 4.046463e-01 |
| sat_bin(29.7,55.3] |  -116.3174438 |      16.387028 |     -7.0981413 | 6.493806e-09 |
| sat_bin(55.3,81.1] |  -143.5428015 |      13.189163 |    -10.8833899 | 2.574926e-14 |

4 rows

It turns out that the rate at which students take the SAT has a moderating effect on this relationship. Consider how things change when we add a categorical variable to make this a parallel slopes model. In this case, we want to separate states into three groups based on how many of their students take the SAT.

First, we use the `cut()` function to add a new `sat_bin` variable to our data frame. Then we fit the parallel slopes model. Note that in this case the categorical variable `sat_bin` is not binary—it has three levels. This results in another coefficient. How many lines to you think we will see in the plot?



### Simpson’s paradox

If you guess three, you were correct.

```
ggplot(data = SAT, aes(x = salary, y = total, color = sat_bin)) + 
  geom_point() + 
  geom_line(data = broom::augment(mod), aes(y = .fitted))
```

![img](https://openintro.shinyapps.io/ims-03-model-07/_w_024758d8/03-07-lesson_files/figure-html/sat-binned-1.png)

But wait, now all three lines have a positive slope!

This phenomenon is known as **Simpson’s paradox**, and it occurs widely in the social and natural sciences. When Simpson’s paradox is present the direction of the relationship between two variables changes if subgroups are considered. Although the y variable may be positively associated with x within multiple groups, it may be the case that y is negatively associated with x when those groups are ignored. When Simpson’s paradox occurs, the group membership is an important confounder that must be controlled for in order to build an appropriate model.



### Consequences of Simpson’s paradox

In the simple linear regression model for average SAT score, (`total`) as a function of average teacher salary (`salary`), the fitted coefficient was -5.02 points per thousand dollars. This suggests that for every additional thousand dollars of salary for teachers in a particular state, the expected SAT score for a student from that state is about 5 points lower.

In the model that includes the percentage of students taking the SAT, the coefficient on `salary` becomes 1.84 points per thousand dollars.

*Hint:* The coefficient on the new term is positive.

Choose the correct interpretation of this slope coefficient.

For every additional thousand dollars of salary for teachers in a particular state, the expected SAT score for a student from that state is about 2 points lower.

For every additional thousand dollars of salary for teachers in a particular state, the expected SAT score for a student from that state is about 2 points higher, after controlling for the percentage of students taking the SAT.

The average SAT score in richer states is about 2 points higher.



### Simpson’s paradox in action

A mild version of Simpson’s paradox can be observed in the MarioKart auction data. Consider the relationship between the final auction price and the length of the auction. It seems reasonable to assume that longer auctions would result in higher prices, since - other things being equal - a longer auction gives more bidders more time to see the auction and bid on the item.

However, a simple linear regression model reveals the opposite: longer auctions are associated with lower final prices. The problem is that all other things are *not* equal. In this case, the new MarioKarts - which people pay a premium for - were mostly sold in one-day auctions, while a plurality of the used MarioKarts were sold in the standard seven-day auctions.

Our simple linear regression model is misleading, in that it suggests a negative relationship between final auction price and duration. However, *for the used* MarioKarts, the relationship is positive.

The object `slr` is already defined for you.

- Fit a simple linear regression model for final auction price (`total_pr`) as a function of duration (`duration`).
- Use `aes()` to add a color aesthetic that’s mapped to the condition variable to the `slr` object, which is the plot shown below.

![img](https://openintro.shinyapps.io/ims-03-model-07/_w_024758d8/03-07-lesson_files/figure-html/unnamed-chunk-12-1.png)

You have successfully completed Lesson 2 in Tutorial 4: Multiple and Logistic Regression.



















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

