## Unusual data points

No discussion of model fit would be complete without a discussion of In our previous discussion of outliers, we learned how to use box plots to identify points that seem to be unusual in the distribution of a single vari. Now, we will refine that understanding by introducing two related but distinct concepts: leverage and influence.

Recall the data we examined previously about Major League Baseball players during the 2010 season. We considered the relationship between the number of home runs hit by each player, and the corresponding number of bases that each player stole. The first statistic is a measurement of power, while the latter is a measurement of speed. As these skills are considered complementary, it should not be surprising that a simple linear regression model has a negative slope. In this case, we have fit the model to only those players with at least 400 at-bats, in a simple attempt to control for the confounding influence of playing time.

```
regulars <- mlbbat10 %>%
  filter(at_bat > 400)

ggplot(data = regulars, aes(x = stolen_base, y = home_run)) +
  geom_point() +
  geom_smooth(method = "lm", se = 0)
```

![img](C:\Users\hammill\Documents\GitHub\bghammill.github.io\ims-03-model\ims-03-lesson-05\images\up2-1.png)

We noted previously that there were two potential outliers here: the point corresponding to the slugger Jose Bautista in the upper left, and the point belonging to speedster Juan Pierre in the lower right.

Now that we have a regression model, we want to think about how individual observations might affect the slope of the line. Typically, the purpose of interpreting the slope coefficient is to learn about the overall relationship between the two variables, so it doesn’t necessarily make sense if one or two individual observations have a disproportionate effect on that slope. Unfortunately, such points of high *leverage* are quite common.

### Leverage

Leverage has a precise mathematical definition that you can see here. The specifics of the formula are not so important, but you should recognize that the leverage score hihi for an observation is entirely a function of the distance between each **individual** value of the explanatory variable and **mean** of the explanatory variable. This means that points that are close to the center of the scatterplot (along the x-axis) have low leverage, while points that are far from the (horizontal) center of the scatterplot have high leverage. For leverage, the yy-coordinate doesn’t matter at all.



hi=1n+(xi−x¯)2∑ni=1(xi−x¯)2



### Leverage computations

It should not be surprising then, that the player with the largest leverage value is the aforementioned Juan Pierre (in the lower right-hand corner of the plot). The leverage scores can be retrieved using the `augment()` function, and then examining the `.hat` variable. [The name comes from the historical convention of computing leverage from the “hat” matrix.] Note that the leverage scores depend only on stolen bases. In this case, Pierre’s leverage score is nearly twice as large as that of the next player.

The `slice_max()` allows for you to **both** order a dataframe and select how many entries you wish to keep. The `order_by` argument allows for you to specify what variable to order by (similar to `arrange()`), and the `n` argument allows for you to specify how many rows you wish to keep. Pretty neat!

```
mod_hr_sb <- lm(home_run ~ stolen_base, data = regulars)

mod_hr_sb %>%
  augment() %>%
  select(home_run, stolen_base, .hat) %>%
  slice_max(order_by = .hat,
            n = 5)
```



| home_run<dbl> | stolen_base<dbl> |  .hat<dbl> |
| ------------: | ---------------: | ---------: |
|             1 |               68 | 0.13081869 |
|             2 |               52 | 0.07033516 |
|             5 |               50 | 0.06416940 |
|            19 |               47 | 0.05550188 |
|             5 |               47 | 0.05550188 |

5 rows

Observations of high leverage, by virtue of their extreme values of the explanatory variable, may or may not have a considerable effect on the slope of the regression line. An observation that does have such an effect is called “influential.” In our case, the regression line is very close to the point corresponding to Juan Pierre. So, even though this is a high leverage observation, it is not considered influential.

### Consider Rickey Henderson…

However, suppose that there was a player with a similar number of stolen bases, but a decent number of home runs as well. In fact, Hall of Famer Rickey Henderson was such a player, and in his MVP-winning season of 1990, he stole 65 bases while hitting 28 home runs. Let’s add this observation to our plot.

![img](C:\Users\hammill\Documents\GitHub\bghammill.github.io\ims-03-model\ims-03-lesson-05\images\up4-1.png)

The original regression line is plotted as the dashed black line, and the new regression line (with Rickey Henderson) is plotted as a solid blue line. Notice how the new regression line pulls upward ever so slightly from the previous dotted regression line. This is a direct result of Henderson’s influence.

Because this is a point of high leverage, it has the ability to pull the slope of the regression line up. However, unlike the point corresponding to Pierre, the point corresponding to Henderson also has a large residual. The combination of high leverage and large residual determine influence.

### Influence via Cook’s distance

In fact, a measurement known as Cook’s distance combines these two quantities (leverage and residual) to measure influence. These figures are also reported by the `augment()` function. We note here that the observation corresponding to Henderson has a large residual, high leverage, and by far the largest value of Cook’s distance.

```
mod_hr_sb <- lm(home_run ~ stolen_base, data = regulars_plus)

mod_hr_sb %>%
  augment() %>%
  select(home_run, stolen_base, .fitted, .resid, .hat, .cooksd) %>%
  slice_max(order_by = .cooksd,
            n = 5)
```



| home_run<dbl> | stolen_base<dbl> | .fitted<dbl> | .resid<dbl> |  .hat<dbl> | .cooksd<dbl> |
| ------------: | ---------------: | -----------: | ----------: | ---------: | -----------: |
|            28 |               65 |     5.769908 |   22.230092 | 0.10551931 |   0.33429976 |
|            54 |                9 |    17.450940 |   36.549060 | 0.00607042 |   0.04210401 |
|            34 |               26 |    13.904913 |   20.095087 | 0.01315006 |   0.02796848 |
|            19 |               47 |     9.524525 |    9.475475 | 0.04971140 |   0.02535189 |
|            39 |                0 |    19.328249 |   19.671751 | 0.01047891 |   0.02124300 |

5 rows

You’ll explore some more outliers in these next exercises.

## Your turn!

The *leverage* of an observation in a regression model is defined entirely in terms of the distance of that observation from the mean of the explanatory variable. That is, observations close to the mean of the explanatory variable have low leverage, while observations far from the mean of the explanatory variable have high leverage. Points of high leverage may or may not be influential.

The `augment()` function from the **broom** package automatically adds the leverage scores (`.hat`) to a model dataframe. Use `augment()` to list the top 6 observations by their leverage scores, in descending order.

### Influence

As noted previously, observations of high leverage may or may not be *influential*. The influence of an observation depends not only on its leverage, but also on the magnitude of its residual. Recall that while leverage only takes into account the explanatory variable (xx), the residual depends on the response variable (yy) and the fitted value (y^y^).

Influential points are likely to have high leverage and deviate from the general relationship between the two variables. We measure influence using Cook’s distance, which incorporates both the leverage and residual of each observation.

Use `augment()` and `slice_max()` to list the top 6 observations by their Cook’s distance (`.cooksd`).

## Dealing with outliers

Previously, we learned about how leverage and influence can help us understand how outliers affect our regression model. Suppose you have determined that an influential observation is affecting the slope of your regression line in a way that undermines the scientific merit of your model. What can you do about it?

![img](C:\Users\hammill\Documents\GitHub\bghammill.github.io\ims-03-model\ims-03-lesson-05\images\o2-1.png)

The short answer is that there isn’t much you can do about it other than removing the outliers. As the statistical modeller, this is a decision you can make, but it’s crucial that you understand the ramifications of this decision and act in good scientific faith.

### The full model

In the full model of all the regular players from 2010 and Rickey Henderson from 1990, the slope of the regression line was -0.21 home runs per stolen base. In other words, players who steal five extra bases hit about one fewer home run, on average.

```
lm(home_run ~ stolen_base, data = regulars_plus) %>%
  coef()
```



### Removing outliers that don’t fit

Now, in this case, there is an easy argument that Rickey Henderson does not fit with the rest of these data. It is a bit of a contrived argument, since we added him previously for effect, but nonetheless there are good reasons to assert that Henderson doesn’t belong. If we remove him, note how the slope of the regression line decreases. Now, it’s only four extra stolen bases that are associated with hitting one fewer home run.

```
## (Intercept) stolen_base 
##  19.7168513  -0.2549133
```

Remember that when removing outliers, the first questions you should ask yourself are:

- What is the justification for removing the observation?
- How does the scope of inference change?

Anytime you are thinking about removing outliers, you should ask yourself what the justification is. “Because it improves my results” is not a good justification. Indeed, conscious ignorance of valid data is not intellectually honest, and has been the cause of more than a few retractions of previously published scientific papers. Be skeptical. The burden of proof is on you to make a strong argument as to why data should be omitted.

Second, you must consider how this changes the scope of inference. If you are studying countries, are you omitting only the poorest countries? If so, then your results no longer apply to all countries, just non-poor countries. Misunderstanding how the scope of inference changes can be a fatal flaw in your analysis.

### Removing outliers that do fit

With Henderson out of the way, we could consider removing Juan Pierre as well. Here, there really aren’t any good arguments as to why this should be done. First, the point is not influential, so whether we include it or not, it won’t affect our results much. More importantly, because Juan Pierre was just a regular major league player in 2010, there is no reason to think that he somehow doesn’t belong to the larger group of players. What is so special about Juan Pierre that would lead us to exclude him? If, hypothetically, he was a pitcher, or he was 60 years old, or he only had one arm, then you could try and make that case. But there is nothing like that going on here, and so we have no scientific reason to exclude him.

Again, ask yourself:

- What is the justification for removing the observation?
- How does the scope of inference change?

```
regulars_new <- regulars %>%
  filter(stolen_base < 60)

lm(home_run ~ stolen_base, data = regulars_new) %>%
  coef()
## (Intercept) stolen_base 
##  19.6869686  -0.2514133
```



## Your turn!

Observations can be outliers for a number of different reasons. Statisticians must always be careful—and more importantly, transparent—when dealing with outliers. Sometimes, a better model fit can be achieved by simply removing outliers and re-fitting the model. However, one must have strong justification for doing this. A desire to have a higher R2R2 is not a good enough reason!



You have successfully completed this tutorial.

# [< Back to Section 3](https://bghammill.github.io/ims-03-model/)



<!-- MathJax -->

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

