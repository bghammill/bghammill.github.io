# More on interaction

Let's take a look at some model results and calculate a few predicted values, so you can see what having an interaction term in a model really means. For this exercise, we are going to use output from 2 regression models predicting the length of an infant at birth (in cm). Each regression model includes as explanatory variable (a) gestational age (in weeks), and (b) an indicator for whether or not a mother experienced toxemia (0/1) during the pregnancy. The difference between the models will be an interaction term.

### Interpretation of estimates, without interaction

The first model did not have an interaction effect between these 2 variables. So the population regression model equation would be:

$$Length = \beta_0 + \beta_1 GestAge + \beta_2 Toxemia + \epsilon$$

And the parameter estimates are:

| Parameter       | Estimate |
| --------------- | -------- |
| Intercept       | 6.28     |
| Gestational Age | 1.07     |
| Toxemia         | -1.78    |

Interpretation of the slope estimates would be as follows:

* Each week of additional gestational age, holding toxemia status constant, is associated with an expected 1.07 cm increase in length at birth
* Having a mother with toxemia is associated with an expected 1.78 cm decrease in length at birth, holding gestational age constant

These interpretations hold regardless of the value of the other variable. We expect a 1.78 cm decrease in length when the mother has toxemia whether the baby was born after 30 weeks or 40 weeks. And we expect a 1.07 cm increase in length for each additional week of gestation whether the mother had toxemia or not.

Adding an interaction between these variable will change all of this and allow these effects to vary based on the value of the other variable. In other words, the effects of each will no longer be constant and *will* depend on the value of the other variable.

### Interpretation of estimates, with interaction

The second model *did* have an interaction effect between these 2 variables. So the population regression model equation would be:

$$Length = \beta_0 + \beta_1 GestAge + \beta_2 Toxemia + \beta_3 GestAge \cdot Toxemia + \epsilon$$

And the parameter estimates are:

| Parameter                 | Estimate |
| ------------------------- | -------- |
| Intercept                 | 6.61     |
| Gestational Age           | 1.06     |
| Toxemia                   | -3.48    |
| Gestational Age x Toxemia | 0.06     |

Interpretation of the slope estimates are now more complicated. 

#### Interpretation of slope for gestational age

Let's work out the effect of a 1-unit increase in gestational age.

When gestational age = *x*, the predicted value is:

$$Length_{Week=x} = b_0 + b_1 x + b_2 Toxemia + b_3 x \cdot Toxemia$$

When gestational age = *x* + 1, the predicted value is:

$$Length_{Week=x+1} = b_0 + b_1 (x + 1) + b_2 Toxemia + b_3 (x + 1) \cdot Toxemia$$

$$Length_{Week=x+1} = b_0 + b_1 x + b_1 + b_2 Toxemia + b_3 x \cdot Toxemia + b_3 Toxemia$$

 So the difference is:

$$Length_{Week=x+1}- Length_{Week=x} = b_1 + b_3 Toxemia  = 1.06 + 0.06 Toxemia$$

since the $$b_0$$, $$b_1 x$$, $$b_2 Toxemia$$, and $$b_3 x \cdot Toxemia$$ terms cancel.

You should be able to see and infer a few things from this equation:

* Most importantly, the effect of each additional week of gestation depends on whether of not the mother had toxemia.
* If the mother did not have toxemia (= 0), each additional week of gestation is associated with an expected increase in length of 1.06 cm
* If the mother did have toxemia (= 1), each additional week of gestation is associated with an expected increase in length of 1.12 cm (b/c 1.06 + 0.06 = 1.12)

There are only 2 possible values of toxemia, so this represents all the possible slopes for the line in these data.

#### Interpretation of slope for toxemia

Now let's work out the effect of toxemia. Since there are only 2 possible values of toxemia (0 and 1), we can work with those values directly.

When toxemia = 0, the predicted value is:

$$Length_{Tox=0} = b_0 + b_1 GestAge + b_2 (0) + b_3 GestAge \cdot (0) = b_0 + b_1 GestAge$$

When toxemia = 1, the predicted value is:

$$Length_{Tox=1} = b_0 + b_1 GestAge + b_2 (1) + b_3 GestAge \cdot (1) = b_0 + b_1 GestAge + b_2 + b_3 GestAge$$

 So the difference is:

$$Length_{Tox=1} - Length_{Tox=0} = b_2 + b_3 GestAge = -3.48 + 0.06 GestAge$$

since the $$b_0$$ and $$b_1 GestAge$$ terms cancel.

You should be able to see and infer a few things from this equation:

* Most importantly, the effect of a mother having toxemia depends on the number of weeks gestation. This means that the $$b_2$$ term is never interpretable on its own since gestational age is always  >0, bringing the interaction term into play.

* For a baby born after 30 weeks gestation, the effect of the mother having toxemia is associated with an expected decrease in length of 1.68 cm (b/c –3.48 + 0.06(30) = –1.68)
* For a baby born after 40 weeks gestation, the effect of the mother having toxemia is associated with an expected decrease in length of 1.08 cm (b/c –3.48 + 0.06(40) = –1.08)

But these are only 2 of the possible values of gestational age. Each different value will lead to a different estimate for the effect of toxemia on length! 

### Try it yourself

It may be helpful to actually see the predicted values for a few specific cases to see how these calculations work. Using the Model 2 regression output above, determine the predicted values for the following six pairs of values:

*Note that there is an Excel workbook in Sakai (Resources > Analysis Tools) called Interaction-calc.xlsx that will get you the predicted values for different entered explanatory values easily, if you'd like. It shows how the calculations work.*

| Observation | Gestational Age (weeks) | Toxemia (0/1) | Predicted length |
| ----------- | ----------------------- | ------------- | ---------------- |
| (1)         | 30                      | 0             |                  |
| (2)         | 30                      | 1             |                  |
| (3)         | 40                      | 0             |                  |
| (4)         | 40                      | 1             |                  |
| (5)         | 41                      | 0             |                  |
| (6)         | 41                      | 1             |                  |

Based on the predicted lengths above, you can:

* Confirm the effect of an additional week of gestation when toxemia status is No by comparing the predicted lengths from Scenarios (5) and (3)
* Confirm the effect of an additional week of gestation when toxemia status is Yes by comparing the predicted lengths from Scenarios (6) and (4)
* Confirm the effect toxemia when weeks of gestation is 30 by comparing the predicted lengths from Scenarios (2) and (1)
* Confirm the effect toxemia when weeks of gestation is 40 by comparing the predicted lengths from Scenarios (4) and (3)



You have successfully completed this tutorial.

# [< Back to Section 3](https://bghammill.github.io/ims-03-model/)



<!-- MathJax -->

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

