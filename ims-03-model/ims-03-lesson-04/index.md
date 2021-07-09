# Lesson 4

## Data set-up

### Is that textbook overpriced?

We’ve spent some time building our graphical intuition about regression, as well as learning the mathematical specification of the model. Now it’s time to focus on what is often the most important thing about a regression model: interpreting the value of the coefficients.

Data were collected on 73 textbooks required for a few randomly selected courses at UCLA. For each textbook, we know the retail price at both the UCLA bookstore and on Amazon.com. We also know the department and course number for each corresponding course, and the ISBN of the book.

> What factors are associated with the price of each book at the UCLA bookstore?

```
glimpse(textbooks)
```



### Compared to the course number?

One might suppose that more advanced books cost more. Our best guess for the level of the course is to extract the course number. The scatterplot shows the relationship between the course number and the price of the book at the UCLA bookstore.

```
textbooks %>%
  ggplot(aes(x = course_number, y = ucla_new)) +
    geom_point()
```

![img](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-04/_w_925f5d3d/03-04-lesson_files/figure-html/2-1.png)

This relationship is very weak, and if anything it appears to be negative.



### Compared to Amazon?

Instead, since Amazon.com provides a ready alternative for UCLA students, let’s consider the relationship between the prices of these books at Amazon, relative to their price at the UCLA bookstore.

```
ggplot(data = textbooks, aes(x = amaz_new, y = ucla_new)) +
  geom_point()
```

![img](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-04/_w_925f5d3d/03-04-lesson_files/figure-html/3-1.png)

Here we see clear evidence of a strong, positive, linear relationship.

As noted previously, the regression line can be added to the plot with the `geom_smooth()` command. While this provides us with the a way to visualize our model, it doesn’t actually tell us what the fitted coefficients are.

```
ggplot(data = textbooks, aes(x = amaz_new, y = ucla_new)) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE)
```

![img](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-04/_w_925f5d3d/03-04-lesson_files/figure-html/4-1.png)



## Slope & intercept interpretation

To get those values, we’ll use the `lm()` command to actually fit the model. We specify two arguments to `lm()`:

1. a `formula` that indicates which variable is the response and which is the explanatory
2. a `data` argument that identifies the data frame where those variables are located

```
lm(ucla_new ~ amaz_new, data = textbooks)
## 
## Call:
## lm(formula = ucla_new ~ amaz_new, data = textbooks)
## 
## Coefficients:
## (Intercept)     amaz_new  
##       0.929        1.199
```

The output from `lm()` reminds us how we called it, and then gives us the fitted coefficients. The value of the intercept coefficient—beta^0beta^0—is $0.93, while the value of the slope coefficient—β^1β^1—is $1.20.

### Regression equation



uclanewˆ=0.929+1.199⋅amazonnewuclanew^=0.929+1.199⋅amazonnew



What this says is that for each additional dollar that Amazon charges for a book, the mean price of books at the UCLA bookstore increase by about $1.20. In effect, the mean price of books at the UCLA bookstore is about 20% higher than those on Amazon.

Generally, we are most interested in the slope coefficient, but occasionally the intercept coefficient is interesting as well. In this case, the intercept is not interesting: it merely tells us that we should expect a book that retails for 0 dollars on Amazon to cost about 93 cents at the UCLA bookstore. This, of course, is irrelevant. Furthermore, since the least expensive book present in the data set cost $8.60, to discuss a book that was free on Amazon would be to extrapolate our findings to values outside the range of the data we collected. This is always dangerous because we can never know whether a model is appropriate outside of the range of the dataset.



### Units and scale

When interpreting slope coefficients, one must pay careful attention to units and scales. Note that the units of the slope coefficient is the units of the response variable per unit of the explanatory variable. In this case, the prices at both bookstores are in dollars, but that is easily changed.

Here, we create a new variable for the price at Amazon in cents, and re-fit the model. Note that while the coefficient has changed, the underlying meaning has not. Here, we say that for each additional cent that a book costs on Amazon, the expected price at the UCLA bookstore increases by about 0.01199 dollars, or 1.2 cents. Thus, in both cases, the price of a book at the UCLA bookstore is about 20% higher, on average, than the corresponding price on Amazon.com.

```
textbooks <- textbooks %>%
  mutate(amaz_new_cents = amaz_new * 100) 
lm(ucla_new ~ amaz_new_cents, data = textbooks)
## 
## Call:
## lm(formula = ucla_new ~ amaz_new_cents, data = textbooks)
## 
## Coefficients:
##    (Intercept)  amaz_new_cents  
##        0.92897         0.01199
```

It’s time for you to start fitting your own regression models.



## Your turn!

Recall that the fitted model for the poverty rate of U.S. counties as a function of high school graduation rate is:



povertyˆ=64.594−0.591⋅hsgradpoverty^=64.594−0.591⋅hsgrad



Which of the following is the correct interpretation of the slope coefficient?

Among U.S. counties, each additional percentage point increase in the poverty rate is associated with about a 0.591 percentage point decrease in the mean high school graduation rate.

Among U.S. counties, each additional percentage point increase in the high school graduation rate is associated with about a 0.591 percentage point decrease in the mean poverty rate.

Among U.S. counties, each additional percentage point increase in the high school graduation rate is associated with a 0.591 percentage point decrease in the poverty rate for each school.

Among U.S. counties, a 1% increase in the high school graduation rate is associated with about a 0.591% decrease in the poverty rate.



### Interpretation in context

A politician interpreting the relationship between poverty rates and high school graduation rates implores his constituents:

> If we can lower the poverty rate by 59%, we’ll double the high school graduate rate in our county (i.e. raise it by 100%).

Which of the following mistakes in interpretation has the politician made?

Implying that the regression model establishes a cause-and-effect relationship.

Switching the role of the response and explanatory variables.

Confusing percentage change with percentage point change.

All of the above.

None of the above.



## Fitting simple linear models

While the `geom_smooth(method = "lm")` function is useful for drawing linear models on a scatterplot, it doesn’t actually return the characteristics of the model. As suggested by that syntax, however, the function that creates linear models is `lm()`. This function generally takes two arguments:

- A `formula` that specifies the model
- A `data` argument for the data frame that contains the data you want to use to fit the model

The `lm()` function return a model object having class `"lm"`. This object contains lots of information about your regression model, including the data used to fit the model, the specification of the model, the fitted values and residuals, etc.

For this exercise, fit the following regression models:

1. Using the `bdims` dataset, create a linear model for the weight of people (`wgt`) as a function of their height (`hgt`).
2. Using the `mlbBat10` dataset, create a linear model for `slg` (y) as a function of `obp` (x).
3. Using the `mammals` dataset, create a linear model for the body weight of mammals (`body_wt`) as a function of their brain weight (`brain_wt`), after taking the natural log of both variables.



### Units and scale

In the previous examples, we fit two regression models:

wgtˆ=−105.011+1.018⋅hgtwgt^=−105.011+1.018⋅hgt

and





slgˆ=0.009+1.110⋅obpslg^=0.009+1.110⋅obp



Which of the following statements is **incorrect**?

A person who is 170 cm tall is expected to weigh about 68 kg.

Because the slope coefficient for `obp` is larger (1.110) than the slope coefficient for `hgt` (1.018), we can conclude that the association between `obp` and `slg` is stronger than the association between height and weight.

None of the above.



## A linear model object

Previously, we learned how to fit a regression model using the `lm()` command. However, we didn’t do much with it—we only displayed the fitted coefficients in the console. The output from `lm()` is an object, and there are a lot of useful things that you can do with that object. To get started, we need to store the output from `lm()` as an object in our environment, in this case aptly named `books_mod`.

```
books_mod <- lm(ucla_new ~ amaz_new, data = textbooks)
```

Note that `mod` is of class `lm`. This object contains all of the information needed to fit our regression model, including (by default) the relevant data, and lots of other pieces of information that we can extract in various ways. It’s worth repeating that `books_mod` is an object of type `lm`—it’s not a `data.frame`, or a `function`, or a `matrix`, or a `vector`.

```
class(books_mod)
## [1] "lm"
```

### Print

By default, when you try to output an `lm` object, you see the “call” (the formula used to fit the model), as well as the fitted coefficients.

```
books_mod
```



### Fitted coefficients

You can also return just the fitted coefficients as a vector using the `coef()` function. This function takes a `lm` object as an input and outputs the coefficients from the model. For our purposes, these are the pieces of information that we are most interested in.



### Summary

In this tutorial, we treat regression as a descriptive statistical technique—thus explaining our focus on the coefficients. In a later tutorial, you will learn about inference for regression. In that application, there is a whole host of other pieces of information about your regression model that you’ll want to inspect. The `summary()` function displays these. Just about every statistical software package has a function that displays a similar table of outputs for a regression model.

However, we won’t dive into this at the moment.

```
summary(books_mod)
```



### Fitted values

Since the object `books_mod` contains everything R knows about our model, we can ask R for the fitted values, using the `fitted.values()` function. This returns a vector containing the y^y^ values for each observation in our dataset.

In general, the length of the vector of fitted values is the same as the number of rows in the original data frame, since each observation corresponds to exactly one value of y^y^. However, if there were any observations with missing data, those will be automatically discarded by R when the model is fit, and thus, the length of the vector of fitted values may not be a large as the number of rows in the original data frame.

```
fitted.values(books_mod)
```



### Residuals

Similarly, each fitted value generates a residual. This residual is the difference between the actual observed value of the response variable, and the expected value of the response according to our model. These residuals can be retrieved using the `residuals()` function, which returns the vectors of residuals.

```
residuals(books_mod)
```



### The broom package

The R ecosystem is constantly evolving. In this tutorial—and series of tutorials—we have been working with a set of tools called the **tidyverse**. One of the packages in the tidyverse is called **broom**, since its goal is to help you tidy up a bit.

By loading the **broom** package and then running the `augment()` function on our `lm` model object, we recover a `data.frame` that contains our original response and explanatory variable, along with the fitted values, residuals, leverage scores, and several other pieces of information relevant to each observation. Working with these tidy data frames can simplify some of the work we do with our models after they are fit.

```
augment(books_mod)
```

## Your turn!

An `"lm"` object contains a host of information about the regression model that you fit. There are various ways of extracting different pieces of information.

The `coef()` function displays only the values of the coefficients. Conversely, the `summary()` function displays not only that information, but a bunch of other information, including the associated standard error and p-value for each coefficient, the R2R2, adjusted R2R2, and the residual standard error. The summary of an `"lm"` object in R is very similar to the output you would see in other statistical computing environments (e.g. Stata, SPSS, etc.)

We have already created the `hgt_wgt_mod` object, a linear model for the weight of individuals as a function of their height, using the `bdims` dataset and the code

```
hgt_wgt_mod <- lm(wgt ~ hgt, data = bdims)
```

Now, you will:

- Use `coef()` to display the coefficients of `hgt_wgt_mod`.
- Use `summary()` to display the full regression output of `hgt_wgt_mod`.

R Code Start Over Solution

 Run Code

1

2

3

\# Show the coefficients

\# Show the full model output

### Fitted values and residuals

Once you have fit a regression model, you are often interested in the fitted values (y^iy^i) and the residuals (eiei), where ii indexes the observations. Recall that:



ei=yi−y^iei=yi−y^i



The least squares fitting procedure guarantees that the mean of the residuals is zero. At the same time, the mean of the fitted values must equal the mean of the response variable.

In this exercise, we will confirm these two mathematical facts by accessing the fitted values and residuals with the `fitted.values()` and `residuals()` functions, respectively, for the following model:

```
hgt_wgt_mod <- lm(wgt ~ hgt, data = bdims)
```

`mod` (defined above) is available in your workspace. We will use the `all.equal()` function to confirm that the mean of the body weights equals the mean of the fitted values of `hgt_wgt_mod`. This function takes two inputs (separated by a comma) that you would like to check if they are equal (up to a tolerance of 1.5e-8).

*Hint*: Remember, you can extract a specific column of a `data.frame` using the `$`.

Next, compute the mean of the residuals of `hgt_wgt_mod` and check if it is equal to 0.



### Tidying your linear model

As you fit a regression model, there are some quantities (e.g. R2R2) that apply to the model as a whole, while others apply to each observation (e.g. y^iy^i). If there are several of these per-observation quantities, it is sometimes convenient to attach them to the original data as new variables.

The `augment()` function from the **broom** package (which is already loaded for you) does exactly this. It takes a model object as an argument and returns a data frame that contains the data on which the model was fit, along with several quantities specific to the regression model, including the fitted values, residuals, leverage scores, and standardized residuals.

The same linear model from the last exercise, `hgt_wgt_mod`, is available in your workspace.

- Create a new data frame called `hgt_wgt_tidy` that is the `augment`ation of the `hgt_wgt_mod` linear model.
- View the `hgt_wgt_tidy` data frame using `glimpse()`.



## Using your linear model

### Is that textbook overpriced?

Recall our previous example about textbooks at the UCLA bookstore. We fit the regression model using the `lm()` command, and stored the resulting model object.

```
books_mod <- lm(ucla_new ~ amaz_new, data = textbooks)
```



### Examining residuals

By examining the residuals, we can learn about whether particular textbooks appear to be under- or over-priced. In this case, the most overpriced book cost $197 at the UCLA bookstore, but just $131 on Amazon—a markup of $66! The model predicts a cost of $158, resulting in a residual of $39.

```
augment(books_mod) %>%
  arrange(desc(.resid)) %>%
  head()
```



### Markup

This turns out to be the management textbook “Financial Statement Analysis and Security Valuation”. Does this qualifies as irony?

```
textbooks %>%
  filter(ucla_new == 197)
```



| dept_abbr<fct> | course<chr> | isbn<fct>      | ucla_new<dbl> | amaz_new<dbl> | more<fct> | diff<dbl> |      |
| :------------- | :---------- | :------------- | ------------: | ------------: | :-------- | --------: | ---- |
| Mgmt           | 228         | 978-0073379661 |           197 |           131 | Y         |        66 |      |

1 row | 1-7 of 9 columns

![img](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-04/_w_925f5d3d/images/FSSV.png)



### Making predictions

What about textbooks that aren’t in our original data set? Using our model to make predictions about new observations—so-called “out-of-sample” observations—is a powerful technique fundamental in machine learning.

For example, the OpenIntro book “Introductory Statistics with Randomization and Simulation” sells for $8.49 on Amazon. What would our model predict is the retail price at the UCLA bookstore?

![img](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-04/_w_925f5d3d/images/ISRS.png)



### New data

The `predict()` function, when applied to an `lm` object, will return the fitted values for the original observations by default. However, if we specify the `newdata` argument, we can use the model to make predictions about any observations we want. Note that the object passed to `newdata` must be a `data.frame` that has a variable with the same name as the explanatory variable used to fit the model. Note also that the output of the `predict()` function is a vector of fitted values.

Here, we create a `data.frame` with one variable and one observation for the ISRS book.

```
new_data <- data.frame(amaz_new = 8.49)
predict(books_mod, newdata = new_data)
##        1 
## 11.10849
```

The model returns that the expected price at the UCLA bookstore is $11.11. We don’t actually know what it sells for at UCLA, but at Smith College it is selling for $11.40, a slightly steeper markup.



### Visualize new observations

Alternatively, the `augment()` function from **broom** will also take a `newdata` argument. However, this function will return a `data.frame`. This is useful if you want to do a bit more with your predictions. Here, we first use `augment()` to create a new `data.frame` of predicted values, and then use `geom_point()` to put those observations on the scatterplot of the original data. Here the single observation for the ISRS book is shown in red.

```
isrs <- augment(books_mod, newdata = new_data)
ggplot(data = textbooks, aes(x = amaz_new, y = ucla_new)) + 
  geom_point() + 
  geom_smooth(method = "lm") +
  geom_point(data = isrs, aes(y = .fitted), size = 3, color = "red")
```

![img](https://openintro.shinyapps.io/ims-03-introduction-to-linear-models-04/_w_925f5d3d/03-04-lesson_files/figure-html/lm5-1.png)

Now it’s time for you to make some of your own predictions.



## Your turn!

The `fitted.values()` function or the `augment()`-ed data frame provides us with the fitted values for the observations that were in the original data. However, once we have fit the model, we may want to compute expected values for observations that were **not** present in the data on which the model was fit. These types of predictions are called *out-of-sample* predictions.

The `ben` data frame contains a height and weight observation for one person. The `hgt_wgt_mod` object contains the fitted model for weight as a function of height for the observations in the `bdims` dataset. We can use the `predict()` function to generate expected values for the weight of new individuals. We must pass the data frame of new observations through the `newdata` argument.

The same linear model, `mod`, is defined in your workspace.

- Output `ben` to the console to take a look at it!
- Use `predict()` with the `newdata` argument to compute the expected weight of the individual in the `ben` data frame.

R Code Start Over Hints

 Run Code

1

2

3

\# Print ben

\# Predict the weight of ben

### Adding a regression line to a plot manually

The `geom_smooth()` function makes it easy to add a simple linear regression line to a scatterplot of the corresponding variables. And in fact, there are more complicated regression models that can be visualized in the data space with `geom_smooth()`. However, there may still be times when we will want to add regression lines to our scatterplot manually. To do this, we will use the `geom_abline()` function, which takes `slope` and `intercept` arguments. Naturally, we have to compute those values ahead of time, but we already saw how to do this (e.g. using `coef()`).

The `coefs` dataframe contains the model estimates retrieved from `coef()` function. Passing this to `geom_abline()` as the `data` argument will enable you to draw a straight line on your scatterplot.

Use `geom_abline()` to add a line defined in the `hgt_wgt_coefs` data frame to a scatterplot of weight vs. height for individuals in the `bdims` dataset.



You have successfully completed Lesson 4 in Tutorial 3: Introduction to Linear Models.





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
