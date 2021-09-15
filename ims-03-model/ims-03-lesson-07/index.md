# Evaluating and extending the parallel slopes model

This lesson covers model evaluation and interaction terms. By looking at different properties of the model, including the adjusted $$R^2$$, you’ll learn to compare models so that you can select the best one. You’ll also learn about adding interaction terms to linear models for added flexibility in fit.

Let's re-load the CARS data first:

```
* Initialize this SAS session;
%include "~/my_shared_file_links/hammi002/sasprog/run_first.sas";

* Makes and checks a working copy of CARS data;
%use_data(cars);
%glimpse(cars);
```

## Model fit, residuals, and prediction

### Residuals

![img](images/mpg-displ-resid-1.png)

One of the most natural questions we might have about our model is: “How well does it fit?” We measure this by computing—for each observation—the difference between the actual value of the response variable and the fitted value from our model. This distance is called a **residual**. Residuals for two observations are illustrated in this plot with vertical arrows. Just as in simple linear regression, the model fitting procedure will automatically minimize the length of those arrows across all of the points.

If you want to look at the model-based residuals for individual observations, we can output those from SAS as:

```
* Regression of HWY_MPG by ENG_SIZE + SUV,
* output predicted values and residuals;
proc reg data=cars;
	model hwy_mpg = eng_size suv;
	output out=cars_pred predicted=pred residual=resid;
run;
```

Have a look at a few records:

```
* Print 10 sample records with model input and output;
proc print data=cars_pred(obs=10);
	var name hwy_mpg eng_size suv pred resid;
run;
```

Here, we are showing the model outcome (`hwy_mpg`), the model inputs (`eng_size`, `suv`), and some model outputs (`pred` for predicted values and `resid` for residual values).

Back to the scatterplot, in a parallel slopes model, our model is represented by two lines, but only one of the lines is relevant for each observation. In the plot, the red points—each corresponding to an SUV—are compared to the red line, and the blue points—each corresponding to a non-SUV—are compared to the blue line.

Note that this necessarily makes our parallel slopes model more flexible than a simple linear regression model. The two lines are constrained to be parallel, but we still have two lines instead of one. If the best fit occurs when the two lines are very close together, then the coefficient of the SUV variable will be very small, and the parallel slopes model will be very similar to the simple linear regression model. If the best fit occurs when the two lines are clearly differentiated, then the coefficient of the SUV variable will be "large", and the parallel slopes model will be adding explanatory value, when compared to the simple linear regression model.

### Model Fit

It would be nice to have a way to compare model fit between the simple linear regression model (with engine size as the only predictor) and the parallel slopes regression model (with engine size and SUV as the predictors).

The coefficient of determination—usually called R-squared or $$R^2$$—carries over from simple linear regression, so this might work. Recall that the sum of the squared residuals (or errors) is denoted SSE. If the model fits the data better, then the residuals are smaller, the SSE is smaller, and the $$R^2$$ value is higher. The total sum of the squares—denoted SST—is a function of the response variable alone and does not depend on the model.

In general, a higher $$R^2$$ may be a sign of a better model fit, but the situation becomes more complicated in multiple regression because *additional explanatory variables will always increase* $$R^2$$. Thus, model fits in multiple regression are often compared using the *adjusted* $$R^2$$ value, defined as:

$$R^{2}_{adj} = 1 − \frac{SSE(n-1)}{SST(n - p - 1)}$$ 

or

$$R^{2}_{adj} = 1 − \frac{(1 - R^2)(n-1)}{(n - p - 1)}$$ 

Note that the only difference from the basic $$R^2$$ formula is that a penalty is applied as the number of explanatory variables, *p*, increases. (*p* does not count the intercept, only the number of explanatory variables.) Unlike $$R^2$$, adjusted $$R^2$$ will not necessarily increase as new explanatory variables are added—it could go up or down because of this penalty term.



*Compare R2 for parallel slopes vs. eng_size model only*

*Available by default in SAS output*



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

![img](images/mpg-interaction-1.png)

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





You have successfully completed this tutorial.

# [< Back to Section 3](https://bghammill.github.io/ims-03-model/)



<!-- MathJax -->

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

