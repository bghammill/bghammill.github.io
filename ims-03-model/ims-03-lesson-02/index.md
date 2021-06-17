# Lesson 2

### Correlation

In the previous lesson, we learned how to visually assess the *strength* of the relationship between two variables by examining a scatterplot. Correlation is a way to quantify the strength of that linear relationship.

The correlation coefficient is a number between -1 and 1 that indicates the strength of a linear relationship. The *sign* of the correlation coefficient corresponds to the direction—positive or negative. The *magnitude* of the correlation corresponds to the strength–values near 0 are weaker and values near 1 or -1 are stronger.

### Near perfect correlation

A correlation coefficient of close to 1 indicates near-perfect positive correlation, as we see in this plot.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/plot-1.png)

### Strong

For scatterplots in which the points are more spread out, the value of the correlation coefficient is lower. Here we see a scatterplot with correlation that we might call “strong.”

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/cor-1.png)



### Moderate

Values of the correlation coefficient that are closer to 0.5 might be considered “moderate”…

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/cor2-1.png)

### Weak

…while values of the correlation coefficient closer to 0.2 might be considered “weak.”

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/cor3-1.png)



### Zero

If there really is no linear relationship between the two variables, then the correlation coefficient will be close to zero. This scatterplot shows two variables that are relatively uncorrelated. Note how knowing one of the xx values gives you very little information about where the corresponding value of yy will be.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/cor4-1.png)



### Negative

Alternatively, when the direction of the relationship is negative, the value of the correlation coefficient is negative as well.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/cor5-1.png)



## Non-linear relationships

It is crucial to remember that the correlation coefficient only captures the strength of the *linear* relationship between two variables. It is quite common to encounter variables that are strongly-related, but in a non-linear way. Blindly computing and reporting the correlation between such variables **does not** yield meaningful results.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/plot2-1.png)

Here, this scatterplot shows a clear, quadratic relationship. To simply interpret the low correlation coefficient as evidence that xx and yy are unrelated would be plainly incorrect. Here, xx and yy are very closely related—just not in a linear fashion.

### Non-linear correlation

Here is an example of real data from a 10-mile run. The pace of the top 10 finishers in each age group are plotted against their age. Note how the relationship between the pace and age is non-linear: 25-year-olds tend to actually be faster than 20-year-olds, but as people age beyond their thirties, they tend to get slower. This pattern is present for both men and women.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/code1-1.png)

The value of the correlation coefficient here is about 0.718, but we have some intuitive sense that pace and age are more closely-related than the figure indicates.



## Calculating the Correlation

There are several different ways that correlation can be defined in statistics, but by far the most common is the **Pearson Product-Moment correlation**. When we say “correlation” we are talking about this value, but you should be aware that there are other definitions that are preferred in other contexts.



r(x,y)=Cov(x,y)SXX⋅SYY−−−−−−−−√r(x,y)=Cov(x,y)SXX⋅SYY



Correlation is most often denoted with the letter rr, and it is a function of two variables, most commonly xx and yy. The definition in terms of covariance and the sums of squares of xx and yy, which we haven’t defined.

### Pearson Product-Moment Correlation

This is an alternative definition using only xx’s and yy’s, and their means: x¯x¯ and y¯y¯. In the denominator, we see the sums of the squared deviations in both xx and yy. In the numerator, we see a sum of terms involving both xx and yy. In fact, each term in the sum is the area of the rectangle with corners at each data point (xi,yi)(xi,yi) and the means (x¯,y¯)(x¯,y¯). So in some sense this is measuring the deviation from the mean in both xx and yy.



r(x,y)=∑ni=1(xi−x¯)(yi−y¯)∑ni=1(xi−x¯)2⋅∑ni=1(yi−y¯)2r(x,y)=∑i=1n(xi−x¯)(yi−y¯)∑i=1n(xi−x¯)2⋅∑i=1n(yi−y¯)2



In practice, we always have R do these computations for us, so memorizing the formulas is not important. For some of you, the mathematics may help reinforce your intuition about what the correlation coefficient actually measures. For others, it may simply be a confusing blur of symbols that can be safely ignored. Having a solid conceptual understanding of correlation is the important thing.

You’ll get some more practice computing correlation in the next set of exercises.

### Using R to compute the correlation

The `cor()` function will compute the Pearson product-moment correlation between variables, `x` and `y`. Since this quantity is symmetric with respect to `x` and `y`, it doesn’t matter which order you input the variables.

At the same time, the `cor()` function is very conservative when it encounters missing data (e.g. `NA`s). If either variable (x or y) has missing values, the `cor()` function will output `NA`.

The `use` argument allows you to override the default behavior of returning `NA` whenever any of the values encountered is `NA`. Setting the `use` argument to `"pairwise.complete.obs"` allows `cor()` to compute the correlation coefficient for those observations where the values of `x` and `y` are both not missing.

In this exercise, you will use `cor()` to compute the correlation between the birthweight of babies (`weight`) in the `ncbirths` dataset and their mother’s age (`mage`). There is no missing data in either variable, so you can use the plain `cor()` function to do this.

Then, you will use `cor()` to compute the correlation between the birthweight (`weight`) and the number of weeks of gestation (`weeks`) for all non-missing pairs of observations

## The Anscombe dataset

### Anscombe

In 1973, statistician Francis Anscombe created a synthetic data set that has been used extensively to illustrate concepts related to correlation and regression.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/ans-1.png)

Here we see four datasets, each having a very different graphical presentation. However, these datasets have the same number of points, the same mean and standard deviation in both xx and yy, the same correlation, and the same regression line. How is it possible that four datasets with such obvious visual differences could have so many identical important properties?

The first set looks the most like “real data.” Here we see a positive, linear, moderately strong relationship. As you gain experience working with real data, you will see lots of scatterplots that look similar to this one. Here, the correlation coefficient of 0.82 is giving us an accurate measurement of the strength of the linear relationship between xx and yy.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/ans2-1.png)

However, in the next set we see something quite different. The relationship here is clearly non-linear. But note also that while the value of the correlation coefficient is 0.82, the correlation—in an intuitive sense—is *perfect*. That is, knowing the value of xx tells you the value of yy exactly—there is no statistical noise. It’s just that the relationship is not linear.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/ans3-1.png)

In the third data set, we have an outlier. Here again, our eyes tell us that with the exception of the outlier, the correlation between xx and yy is perfect, and in this case, it is linear. However, the presence of the outlier has lowered the value of the correlation coefficient, and (as you will learn later) the slope of the regression line.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/ans4-1.png)

Finally, in the fourth data set we see a pathological example with almost the opposite problem. In the previous plot, we saw that the presence of a single outlier had lowered the correlation from 1 to 0.82. In this plot, the presence of a single outlier raises the correlation from undefined to 0.82. Note that here, with the exception of the outlier, knowing xx doesn’t tell you anything about yy. It is only the outlier that gives you the appearance of a correlation, but that correlation is very spurious!

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/ans5-1.png)

The Anscombe datasets are fabricated, but they help to illustrate some properties of correlation. Above all else, these examples help to underscore the importance of visually inspecting your data! You never know what you are going to see.

Now it’s your turn to explore the Anscombe data set.

## Your turn!

In 1973, Francis Anscombe famously created four datasets with remarkably similar numerical properties, but obviously different graphic relationships. The `anscombe` dataset contains the `x` and `y` coordinates for these four datasets, along with a grouping variable, `set`, that distinguishes which quartet the observations belong to (e.g. 1, 2, 3, or 4).

It may be helpful to remind yourself of the graphic relationship by viewing the four scatterplots:

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/explot-1.png)

For each of the four `set`s of data points in the `anscombe` dataset, compute the following in the order specified. Names are provided in your call to `summarize()`.

- Number of observations, `N`
- Mean of `x`
- Standard deviation of `x`
- Mean of `y`
- Standard deviation of `y`
- Correlation coefficient between `x` and `y`

## Perception of correlation

Recall the scatterplot between the poverty rate of counties in the United States and the high school graduation rate in those counties from the previous lesson.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/mr3-pre-1.png)



Estimating the value of the correlation coefficient between two quantities from their scatterplot can be tricky. [Statisticians have shown that](https://statistics.stanford.edu/sites/default/files/EFS NSF 206.pdf) people’s perception of the strength of these relationships can be influenced by design choices like the x and y scales. Nevertheless, with some practice your perception of correlation will improve.

We are going to recreate scatterplots from the previous tutorial, jot down your best estimate of the value of the correlation coefficient between each pair of variables, and compare these values to the actual values you compute.

First, we will start with the relationship between `OBP` and `SLG` for all players in the `mlbbat10` dataset.



Next, using the `bdims` dataset, draw the plot then calculate the correlation between height (`hgt`) and weight (`wgt`) *separately for each sex*.



Finally, draw the plot then calculate the correlation between body weight (`body_wt`) and brain weight (`brain_wt`) for all species of `mammals`. Alongside this computation, compute the correlation between the same two quantities after taking their natural logarithms (using the `log()` function).



### Exercise and Beer

The word “correlation” has both a precise mathematical definition and a more general definition for typical usage in English. While these uses of the word are obviously related and generally in sync, there are times when these two uses can be conflated and/or misconstrued. This occurs frequently in the media when journalists write about scientific results—particularly in health-related studies.

For example, consider the following article from the New York Times about the connection between exercise and drinking beer.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/images/nytimes_beer.png)



After discussing some relatable scenarios, the lead-in raises the question of causation: “But whether exercise encourages people to drink and, likewise, whether drinking encourages people to exercise has been in dispute.”

But causation is not really the goal here. Notice how in the next two paragraphs, verbs linking exercise and beer-drinking include: “influence”, “affect”, and “tend to”. The “interplay between” the two activities is also mentioned.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/images/nytimes_beer2.png)



In the end, this article is worded fairly carefully. Although many different possible connections between exercising and drinking are explored, the author has taken steps not to imply that the research demonstrates that exercising causes one to drink, nor that drinking causes one to exercise. The best interpretation is quoted from the actual study: “people drank more than usual on the same days that they engaged in more physical activity than usual.”

Note how this statement makes it clear that *an association was observed*, but does not try to imply causality.

In observational studies–which are especially common in nutritional science, health, and epidemiology–one must always be careful not to erroneously suggest that correlation implies causation.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/images/nytimes_beer3.png)



### NFL arrests

Here is another interesting use of correlation from the New York Times. The article examines the arrest records of National Football League players, and considers whether certain franchises tend to have more or fewer players arrested consistently over time. This use of correlation—often called *serial correlation* or *autocorrelation*—checks to see whether a single numeric variable is highly correlated with past measurements of itself.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/images/nytimes_nfl.png)

From the plot and the reported 0.53 correlation, we see some evidence that the number of players arrested from each team during the seven-year period from 2000 to 2006 was positively associated with the number of players arrested by that same team during the subsequent seven-year period from 2007 to 2013.

The author interprets both the plot and reported “pretty solid” correlation as evidence of a team-specific effect. Without offering explanations or implying cause-and-effect, the author effectively communicates that player arrests in the NFL do not appear to be randomly distributed across teams, but rather that certain teams may have player-acquisition philosophies that result in them having consistently higher numbers of players arrested.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/images/nytimes_nfl2.png)



### Correlation vs. Regression

Sometimes journalists can leave statistical concepts lost in translation. The first sentence of this article reports that no correlation was found between economic growth and lower tax rates at the top of the income bracket. This is a politically-sensitive finding, and indeed much of the article discusses attempts by Senate Republicans to bury these findings, which run counter to their ideological position on macroeconomic issues.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/images/nytimes_tax.png)

Of greater interest to us is that the word “correlation” here is not being used in its precise statistical sense. The link to the actual report reveals that the findings are based on a multiple regression model, not a simple correlation. Multiple regression models are the subject of the next tutorial, but for now, it is enough to know that multiple regression is a technique that extends regression such that more than one explanatory variable can be taken into account. Although you can extend regression to include more than one explanatory variable, this is impossible to do with correlation.



### Can you plot a correlation?

Finally, it is not uncommon to encounter references to a “plot” of a correlation. This doesn’t quite make sense. In this tutorial you’ve seen that a scatterplot is a graphical presentation of two variables, and the strength of a linear relationship shown in a scatterplot can be neatly summarized by a correlation. In this case, the article refers to a table of correlations between several variables–often called a *correlation matrix*–as a “plot” of a correlation.

The authors reached the conclusion that there was no correlation between voting for Brexit and racism, based on the small correlation values. What other evidence would you want to see in order to have confidence in these findings?

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/images/heatst_brexit.png)



## Your turn!

The next few exercises will test your ability to interpret correlation like a pro.

Recall that you previously determined the value of the correlation coefficient between the poverty rate of counties in the United States and the high school graduation rate in those counties was -0.6808085.

![img](https://openintro.shinyapps.io/ims-03-model-02/_w_767d57e3/03-02-lesson_files/figure-html/mc4-pre-1.png)



Choose the correct interpretation of this value.

People who graduate from high school are less likely to be poor.

Counties with lower high school graduation rates are likely to have lower poverty rates.

Counties with lower high school graduation rates are likely to have higher poverty rates.

Because the correlation is negative, there is no relationship between poverty rates and high school graduate rates.

Having a higher percentage of high school graduates in a county results in that county having lower poverty rates.

Submit Answer

### Correlation and causation

In the San Francisco Bay Area from 1960-1967, the correlation between the birthweight of 1,236 babies and the length of their gestational period was 0.408.

Which of the following conclusions **is not** a valid statistical interpretation of these results?

We observed that babies with longer gestational periods tended to be heavier at birth.

It may be that a longer gestational period contributes to a heavier birthweight among babies, but a randomized, controlled experiment is needed to confirm this observation.

Staying in the womb longer causes babies to be heavier when they are born.

These data suggest that babies with longer gestational periods tend to be heavier at birth, but there are many potential confounding factors that were not taken into account.

Submit Answer



## Spurious correlations

Above all else, we must always remember that correlation does not imply causation. That is, just because two variables appear to move together does not mean that changes in one are causing changes in the other. There are many potential confounders that can cloud our ability to determine cause-and-effect.

Remarkable–but nonsensical–correlations are called “spurious.” In fact, there is an entire blog devoted to them. Here is how the number of films Nicolas Cage has appeared in correlates with the number of people who drowned by falling into a pool.

![img](http://tylervigen.com/images/spurious-correlations-share.png)

Are these two variables related by any substantive means? Of course not.

### Spurious over time

Here is another example that traces U.S. oil production to the quality of rock music. We suppose one may need to burn the midnight oil to make a truly endearing rock classic!

![img](http://strata.uga.edu/6370/lecturenotes/images/spuriousCorrelationOilMusic.jpg)

In each of these cases, the correlation was illustrated as two variables that seem to move together over time. In fact, time is an obvious confounder for many of these spurious correlations. Any time you see two variables linked over time, you should be skeptical of the role that time can play as a confounder.



### Spurious over space

This webcomic from XKCD illustrates how space can also be present in spurious correlations. Colored maps–called choropleths–can be visually arresting ways of conveying information, but they can also reveal spurious correlations. Many things occur more often in places where there are more people, so failure to control for the confounding influence of population can lead to spurious correlations.

![img](http://imgs.xkcd.com/comics/heatmap.png)



### Spurious for whatever reason

In other cases it may not be so obvious what confounding variables are driving the spurious correlation. This plot shows that as the number of lemons imported into the U.S. from Mexico has increased over time, the number of U.S. highway fatalities has decreased. The relationship appears to be strong, but there is no plausible causative mechanism that could explain the relationship.

![img](http://www.tutor2u.net/_legacy/blog/files//blog-correlation-291209.gif)

Now it’s time for you to get your hands dirty with some truly spurious correlations.



## Your turn!

Statisticians must always be skeptical of potentially spurious correlations. Human beings are very good at seeing patterns in data, sometimes when the patterns themselves are actually just random noise. To illustrate how easy it can be to fall into this trap, we will look for patterns in truly random data.

The `noise` dataset contains 20 sets of `x` and `y` variables drawn at random from a standard normal distribution. Each set, denoted as `z`, has 50 `x`, `y` pairs. A preview of the `noise` dataset is shown below.

```
glimpse(noise)
## Rows: 1,000
## Columns: 3
## $ y <dbl> -1.20706575, 0.27742924, 1.08444118, -2.34569770, 0.42912469, 0.5060…
## $ x <dbl> -1.20533342, 0.30146674, -1.53914522, 0.63537072, 0.70295177, -1.905…
## $ z <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 2…
```

We will use these simulated data to explore if any pairs of variables might be meaningfully correlated or if the correlation coefficients close to zero. To do this take the following steps:

1. Create a scatterplot that shows the relationship between `x` and `y`, faceted by the set (`z`).

Code Run Code Start Over Solution

1

2

3

4

\# Create faceted scatterplot

ggplot(data = noise, aes(x = x, y = y)) + 

  geom_point() + 

  ___

1. Compute the correlation between each set of `x` and `y`.

Code Run Code Start Over Hints

1

2

3

4

5

6

7

\# Compute correlations for each dataset

noise_summary <- noise %>%

  group_by(___) %>%

  summarize(N = n(), 

​            spurious_cor = cor(___, ___))

noise_summary

1. Identify the datasets that show non-trivial correlation of greater than 0.2 in absolute value.



You have successfully completed Lesson 2 in Tutorial 3: Introduction to Linear Models.





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

