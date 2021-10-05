### Calculating quantiles

Another way to measure how far the observed statistic is from the hypothesize null value is to calculate quantiles of the null statistics. After we've generated 100 different shuffles of the original data, we see that the 5% quantile is -0.208. That is, 5% of the observations are at -0.208 or below. The 95% quantile is 0.208. That is, 95% of the null observations are at 0.208 or below, meaning that our observed statistic of 0.29 is larger than 95% of the null statistics.




### Quantile measurement

Using R, we can get the same quantile measurement that allow comparisons of the null statistics and the observed statistic. Given the previous simulations, we can see that 95% of the null differences are 0.208 or lower. These simulations give further evidence that the observed statistic is not consistent with the bulk of the null differences.

The `quantile()` function often comes in handy! It is a function from "base R" that calculates the quantile of a numerical variable, where the quantile is specified by the `probs` argument. By specifying  `probs = 0.05`, you are telling R that you are interested in the value of `stat` where 5% of the are below it. Similarly for `probs = 0.95`, where 95% of the values of `stat` are below it. 

```
gender_discrimination_perm %>%
  summarize(q.05 = quantile(stat, probs = 0.05),
            q.95 = quantile(stat, probs = 0.95))
```

### Critical region

Often the quantiles describing the tails of the null distribution determine what is called the **critical region**. The critical region determines which observed statistics are considered "consistent" with the null distribution.

```
gender_discrimination_perm %>%
  ggplot() +
  geom_rect(aes(xmin = quantile(stat, probs = 0.05), xmax = quantile(stat, probs = 0.95, type = 6)),
    fill = openintro::COL[1, 4], alpha = .05, ymin = -Inf, ymax = Inf
  ) +
  geom_dotplot(aes(x = stat), binwidth = .02) +
  geom_vline(xintercept = 21 / 24 - 14 / 24, color = openintro::COL[4, 1], lwd = 1.3) +
  geom_vline(xintercept = -(21 / 24 - 14 / 24), color = openintro::COL[4, 1], lwd = 1.3) +
  geom_vline(aes(xintercept = quantile(stat, probs = 0.05)), color = openintro::COL[1, 1], lwd = 1.3) +
  geom_vline(aes(xintercept = quantile(stat, probs = 0.95, type = 6)), color = openintro::COL[1, 1], lwd = 1.3) +
  xlab("Difference in Proportions") +
  theme(
    axis.title.y = element_blank(), axis.text.y.left = element_blank(),
    axis.ticks.y = element_blank(), panel.grid.major.y = element_blank(),
    panel.grid.minor = element_blank(), panel.grid.major.x = element_line()
  ) +
  scale_x_continuous(breaks = seq(-0.5, 0.5, 0.1), limits = c(-0.5, 0.5)) +
  geom_label(label = "5%", color = openintro::COL[1, 1], x = 0.4, y = .9, cex = 6) +
  geom_label(label = "5%", color = openintro::COL[1, 1], x = -0.4, y = .9, cex = 6)

grid.lines(
  x = unit(c(0.7, 0.95), "npc"),
  y = unit(c(.8, 0.8), "npc"),
  gp = gpar(fill = openintro::COL[1, 1], col = openintro::COL[1, 1]),
  arrow = arrow(
    length = unit(0.15, "inches"),
    ends = "last", type = "closed"
  )
)

grid.lines(
  x = unit(c(.31, .05), "npc"),
  y = unit(c(.8, 0.8), "npc"),
  gp = gpar(fill = openintro::COL[1, 1], col = openintro::COL[1, 1]),
  arrow = arrow(
    length = unit(0.15, "inches"),
    ends = "last", type = "closed"
  )
)
```

### Reflecting on analysis

```
question(
  "Based on the plot you created in the last exercise (displayed again here), which of the following seems like a reasonable conclusion about promotions?",
  correct = "Good job! There are very few permuted differences which are as extreme as the observed difference, but there cannot be a causative conclusion because the study was observational.", allow_retry = TRUE,
  answer("In the population there is no evidence that women are discriminated against.", message = "No, try again. Look at the curves to see if the observed data is consistent with the null hypothesis."),
  answer("In the population there is no evidence that women are promoted at a different rate from men.", message = "No, try again. Look at the curves to see if the observed data is consistent with the null hypothesis."),
  answer("In the population there is evidence that women are discriminated against.", message = "Incorrect. Although there is evidence of differential rates of promotion, the study was not set up to understand why the rates of promotion are different."),
  answer("In the population there is evidence that women are promoted at a different rate, but we cannot tell whether the difference is due to discrimination or something else.", correct = TRUE)
)
```

```
# Make a histogram of permuted differences 
# with a red line at the observed difference
ggplot(gender_discrimination_perm, aes(x = stat)) + 
  geom_histogram(binwidth = .01) +
  geom_vline(aes(xintercept = diff_orig), color = "red")
```

### Critical region

It seems as though the statistic—a difference in promotion rates of 0.2917—is on the extreme end of the permutation distribution.
That is, there are very few permuted differences which are as extreme as the observed difference.

To quantify the extreme permuted (null) differences, we use the `quantile()` function.

The dplyr package has been loaded for you.

Using the permuted differences, `gender_discrimination_perm`, find the quantile values of `stat` such that:

- 10% of the permuted differences are above the value (0.9 quantile)
- 5% of the permuted differences are above the value (0.95 quantile)
- 1% of the permuted differences are above the value (0.99 quantile)

```
gender_discrimination_perm %>% 
  summarize(
    # Find the 0.9 quantile of stat
    q.90 = ___(___, probs = ___),
    # ... and the 0.95 quantile
    q.95 = ___,
    # ... and the 0.99 quantile
    q.99 = ___
  )
```

```
gender_discrimination_perm %>% 
  summarize(
    q.90 = quantile(stat, probs = 0.9),
    q.95 = quantile(___, ___ = 0.95),
    q.99 = ___
  )
```


```
gender_discrimination_perm %>% 
  summarize(
    # Find the 0.9 quantile of stat
    q.90 = quantile(stat, probs = 0.9),
    # ... and the 0.95 quantile
    q.95 = quantile(stat, probs = 0.95),
    # ... and the 0.99 quantile
    q.99 = quantile(stat, probs = 0.99)
  )
```

Nice job! You were able to figure out the upper bound of the permuted statistics. Note, different people will define `upper` differently. Different options are to use the top 10% or the top 5% or the top 1%.

### Two-sided critical region

For the discrimination data, the question at hand is whether or not women were promoted less often than men. However, there are often scenarios where the research question centers around a difference without directionality. 

For example, you might be interested in whether the rate of promotion for men and women is *different*. In that case, a difference in proportions of -0.29 is just as "extreme" as a difference of positive 0.29.

If you had seen that women were promoted more often, what would the other side of the distribution of permuted differences look like?  That is, what are the smallest (negative) values of the distribution of permuted differences?

Using the permuted differences, `gender_discrimination_perm`, find the quantile values such that:

- 1% of the permuted differences are below the value (0.01 quantile)
- 5% of the permuted differences are below the value (0.05 quantile)
- 10% of the permuted differences are below the value (0.1 quantile)

```
# Use gender_discrimination_perm
___ %>% 
  # ... to calculate summary stats
  ___(
    # Find the 0.01 quantile of stat
    q.01 = ___,
    # ... and 0.05
    q.05 = ___,
    # ... and 0.1 
    q.10 = ___
  )
```

```
gender_discrimination_perm %>% 
  summarize(
    q.01 = quantile(stat, probs = 0.01),
    q.05 = quantile(stat, probs = ___),
    q.10 = ___
  )
```

```
# Use gender_discrimination_perm
gender_discrimination_perm %>% 
  # ... to calculate summary stats
  summarize(
    # Find the 0.01 quantile of stat
    q.01 = quantile(stat, probs = 0.01),
    # ... and 0.05
    q.05 = quantile(stat, probs = 0.05),
    # ... and 0.1 
    q.10 = quantile(stat, probs = 0.1)
  )
```


Great work! Now you've seen both the upper and lower values of the permuted statistics. The permuted statistics will help you know whether the value calculated from the data (the observed statistic) is large or small.


## Why 0.05?

In the previous exercises, you determined quantiles of the null distribution. The cutoff values allow you to know how big or small the observed statistic should be in order for you to reject the null hypothesis.

The choice of 0.05 for the quantile cutoff is somewhat arbitrary. However, the reasons to use it are historical, ingrained in much of science, and somewhat intuitive.  

The value of 5% as a cutoff should not be used as a way to determine truth. Indeed, the test of significance should be used to figure out which research to replicate and which research should not be pursued. 

We should understand that a 5% cutoff level is historical and arbitrary. However, it is also worth considering that any given level of significance is also personal and subjective.

### Personal level of significance

As it turns out, the 5% cutoff value is consistent with what many of us consider to be unusual. Consider a situation where you as the researcher are trying to decide whether a coin is fair or not.

![](C:\Users\hammill\Documents\GitHub\bghammill.github.io\ims-04-foundations\ims-04-lesson-02\images\lesson2_img8.png)

Would you think that flipping one head is evidence that the coin is unfair? Probably not.

What about two heads? Three heads?

At what number of heads in a row would you start thinking that maybe this coin isn't actually a fair coin?

For many of you, four or five heads in a row would seem unusual. Interestingly, four or five heads in a row correspond to probabilities that are very close to 0.05. Some of you might need six or seven heads in a row before you start to doubt the fairness of the coin. The notion of statistical significance is similar using a cutoff of 0.01 instead of 0.05 indicates a higher degree of skepticism that an observed result is not simply due to random noise. Which is to say that the cutoff of 0.05 is personal and subjective, but not meaningless. 

If you are curious to test your own personal level of significance, try this assessment at [https://www.openintro.org/book/stat/why05/](https://www.openintro.org/book/stat/why05/). It may give you a sense for why we so often use 0.05 for hypothesis testing.

![Why do we so often use 0.05 for hypothesis testing? https://www.openintro.org/book/stat/why05/](C:\Users\hammill\Documents\GitHub\bghammill.github.io\ims-04-foundations\ims-04-lesson-02\images\lesson2_img8b.png)


### How does sample size affect results?

Notice that the observed difference of 0.2917 is in the extreme right tail of the permuted differences. If the **sample was ten times larger** but the sample statistic was exactly the same (i.e. 0.2917), how would the distribution of permuted differences change? Complete the following sentence.

```
question(
  "The statistic of 0.2917 would ______.",
  correct = "Right! The statistic would be much farther to the right of the permuted differences, as you'll see in the next exercise.", allow_retry = TRUE,
  answer("Be closer to the bulk of the permuted differences (within the distribution).", message = "Not quite, a larger sample will lead to a less variable statistic."),
  answer("Remain in the same location (in the right tail of the distribution of permuted differences).", message = "Not quite, a larger sample will lead to a less variable statistic."),
  answer("Be much farther to the right of the permuted differences (completely off of the distribution).", correct = TRUE)
)
```

### Sample size in randomization distribution

We've created two new datasets for you with essentially the same difference in proportions as the original discrimination data. 
However, one of the datasets (`gender_discrimination_small`) is one third the size of the original dataset and the other (`gender_discrimination_big`) is 10 times larger than the original dataset.

Additionally, the same permutation code used previously has been run on the small and big datasets to create small and big distributions of permuted differences in promotion rates (`gender_discrimination_small_perm` and `gender_discrimination_big_perm`, respectively).

In this exercise, you'll use these two new distributions to get a sense for how the differences vary given widely different sample sizes. In particular, notice the range of variability on the x-axis of each plot.

### 

 (1)

- Tabulate the small dataset, `gender_discrimination_small`. That is, call `count()`, passing the `gender` and `promote` columns, to get a contingency table.
- Do the same with the big dataset, `gender_discrimination_big`.

```
# Tabulate the small dataset
gender_discrimination_small %>% 
  # Select gender and promote
  ___(___, ___)
  
# Do the same for gender_discrimination_big
___

```

```
gender_discrimination_small %>% 
  count(gender, decision)
```

```
# Tabulate the small dataset
gender_discrimination_small %>% 
  # Count by gender and promote
  count(gender, decision)
  
# Do the same for gender_discrimination_big
gender_discrimination_big %>% 
  count(gender, decision)
```

### 

 (2)

- Using the small permutation dataset, `gender_discrimination_small_perm`, plot `stat`.
- Add a histogram layer with `binwidth` `0.01`.
- Add a vertical line using `geom_vline()`, with x-axis intercept `diff_orig_small`.


```
# Using gender_discrimination_small_perm, plot stat
ggplot(___, aes(x = ___)) + 
  # Add a histogram layer with binwidth 0.01
  ___(binwidth = ___) +
  # Add a vline layer, crossing x-axis at diff_orig_small
  ___(aes(xintercept = ___), color = "red")
```

```
ggplot(gender_discrimination_small_perm, aes(x = stat)) + 
  geom_histogram(binwidth = 0.01) +
  geom_vline(aes(xintercept = ___), color = "red")
```

```
# Using gender_discrimination_small_perm, plot stat
ggplot(gender_discrimination_small_perm, aes(x = stat)) + 
  # Add a histogram layer with binwidth 0.01
  geom_histogram(binwidth = 0.01) +
  # Add a vline layer, crossing x-axis at diff_orig_small
  geom_vline(aes(xintercept = diff_orig_small), color = "red")
```

 (3)
Draw the same plot again, this time using the big dataset, `gender_discrimination_big_perm` and an x-axis intercept of `diff_orig_big`.


```
# Swap the dataset to gender_discrimination_big_perm
ggplot(___, aes(x = stat)) + 
  geom_histogram(binwidth = 0.01) +
  # Change the x-axis intercept to diff_orig_big
  geom_vline(aes(xintercept = ___), color = "red")
```

```
ggplot(gender_discrimination_big_perm, aes(x = stat)) + 
  geom_histogram(binwidth = 0.01) +
  geom_vline(aes(xintercept = ___), color = "red")
```

```
# Swap the dataset to gender_discrimination_big_perm
ggplot(gender_discrimination_big_perm, aes(x = stat)) + 
  geom_histogram(binwidth = 0.01) +
  # Change the x-axis intercept to diff_orig_big
  geom_vline(aes(xintercept = diff_orig_big), color = "red")
```

Great work! The observed difference is consistent with differences you would see by chance if the sample size was small. The observed difference would virtually never be observed by chance if the sample size was big.

### Sample size for critical region

Using the randomization distributions with the small and big datasets, calculate different cutoffs for significance. Remember, you are most interested in a large positive difference in promotion rates, so you are calculating the upper quantiles of 0.90, 0.95, and 0.99.

A function for calculating these quantiles, `calc_upper_quantiles()` is sown in the script. 
We don't expect for you to learn how to write a function in R! Instead, the function is intended to demonstrate how we can automate the process of finding the 90%, 95%, and 99% quantiles of a test statistic. When you click on the "Run Code" button, you are able to use the function, simply by typing its name (`calc_upper_quantiles()`).  

- As a reference point, run the call to `calc_upper_quantiles()` to calculate the relevant quantiles associated with the original dataset of 1000 permuted differences, `gender_discrimination_perm`.
- Do the same for the small dataset, `gender_discrimination_small_perm` ...
- and for the big dataset, `gender_discrimination_big_perm`.

```
calc_upper_quantiles <- function(dataset) {
  dataset %>% 
    summarize(
      q.90 = quantile(stat, probs = 0.90),
      q.95 = quantile(stat, probs = 0.95),
      q.99 = quantile(stat, probs = 0.99)
    )
}

# Recall the quantiles associated with the original dataset
calc_upper_quantiles(gender_discrimination_perm)

# Calculate the quantiles associated with the small dataset
___

# Calculate the quantiles associated with the big dataset
___
```

```
calc_upper_quantiles <- function(dataset) {
  dataset %>% 
    summarize(
      q.90 = quantile(stat, probs = 0.90),
      q.95 = quantile(stat, probs = 0.95),
      q.99 = quantile(stat, probs = 0.99)
    )
}
# Recall the quantiles associated with the original dataset
calc_upper_quantiles(gender_discrimination_perm)

# Calculate the quantiles associated with the small dataset
calc_upper_quantiles(gender_discrimination_small_perm)

# Calculate the quantiles associated with the big dataset
calc_upper_quantiles(gender_discrimination_big_perm)
```


Great work! Notice how the differences in proportions must be much larger to be "significant" if the sample size is small. With a big sample size, a small difference in proportions can be "significant."

## What is a p-value?

Remember, our interest is in how consistent the observed data are with data taken from a population where gender and promotion rates are delinked. 

By permuting the data repeatedly, we can quantify how likely the observed data are to have happened in a situation where the null hypothesis is true. The null hypothesis is that promotion rates, in the population, do not differ for men and women. 

We know what to expect in terms of variability when promotion rates don't differ and we compare that variability directly with the observed data of 0.2917.

*Observed data*

```
dt <- tibble(gender = c('Female','Male','Total'), Promoted = c(21,14,35), Not_Promoted= c(3,10,13), Total = c(24,24,48)) 

kable(dt, format = "html", escape = F) %>%
  kable_styling("striped", full_width = F, position = "float_left")
```

```
gender_discrimination_perm %>%
  ggplot() +
  geom_dotplot(aes(x=stat), binwidth = .02) + 
  geom_vline(xintercept = 21/24 - 14/24, color = openintro::COL[4,1], lwd=1.3) + 
  geom_vline(xintercept = -(21/24 - 14/24), color = openintro::COL[4,1], lwd=1.3) + 
  geom_vline(aes(xintercept = quantile(stat, probs = 0.05, type=1)), color = openintro::COL[3,1], lwd=1.3) + 
  geom_vline(aes(xintercept = quantile(stat, probs = 0.95, type=6)), color = openintro::COL[3,1], lwd=1.3) + 
  geom_vline(aes(xintercept = quantile(stat, probs = 0.1, type = 1)), color = openintro::COL[3,1], lwd=1.3) + 
  geom_vline(aes(xintercept = quantile(stat, probs = 0.01, type = 1)), color = openintro::COL[3,1], lwd=1.3) + 
  geom_vline(aes(xintercept = quantile(stat, probs = 0.9, type = 6)), color = openintro::COL[3,1], lwd=1.3) + 
  geom_vline(aes(xintercept = quantile(stat, probs = 0.99, type = 6)), color = openintro::COL[3,1], lwd=1.3) + 
  xlab("Difference in Proportions") +
  theme(axis.title.y = element_blank(), axis.text.y.left = element_blank(),
        axis.ticks.y = element_blank(), panel.grid.major.y = element_blank(),
        panel.grid.minor = element_blank(), panel.grid.major.x = element_line()) +
  scale_x_continuous(breaks = seq(-0.5,0.5,0.1), limits = c(-0.5,0.5)) 
```



Recall that the 0.1 quantile is 0.125, the 0.05 quantile is 0.208, and the 0.01 quantile is 0.375. So, depending on the level of significance, we may or may not reject the null hypothesis. To get around the "sometimes reject" problem, we quantify the degree to which the data disagree with the null distribution using something called a p-value.



## Summary of gender discrimination

The observed gender discrimination data is not really consistent with the permuted null differences. Only 30 of the 1000 permuted differences were larger than or equal to the observed statistic. That is, we would have observed data like ours only 3% of the time if men and women were equally likely to be promoted. 

```
gender_discrimination_perm %>%
  summarize(pvalue = mean(diff_orig <= stat)) 
```

```
ggplot(gender_discrimination_perm) +
  geom_histogram(aes(x = stat)) + 
  geom_vline(xintercept = diff_orig, color = openintro::COL[4,1])
```


Pay special attention to how the p-value is computed here. First we identify permuted differences that are larger than or equal to the observed statistic and label those situations TRUE (or a value of 1), all other permutations FALSE (or a value of 0). By averaging the 0s and 1s, we can use the `mean()` to find the proportion of times the permuted difference is larger than or equal to the observed difference. 

Because 0.03 is less than 0.05, we decide to reject the null hypothesis in favor of the alternative, claiming that men are promoted at a higher rate than women. That is, we conclude that it was not simply random variability which led to a higher proportion of men being promoted. A p-value of 0.03 is reasonably close to 0.05 which means we should be somewhat careful in making strong claims. We should take the results as a indication that more work should be done on the claims. Indeed, in 40 years since this research was published, many social scientists have been able to replicate research on gender discrimination in the workforce.

Because the study was randomized, that is, they randomly assigned the resumes to the managers, there is nothing systematically different about the two groups except the name on the resume. As an example, it wouldn't make sense to have given the female resumes to the first 24 managers who arrived at the training. Those early arriving individuals might be less inclined to promote anyone given their strict adherence to being on time.

The only difference in the two groups, both the participants as well as the resumes, was the name on the top of the resume.

Therefore, we can conclude that *any difference in promotion rates is due to the gender of the applicant*. That is, we can infer a __causal__ connection between the gender of the applicant being male and a higher promotion rate.

The 35 individuals in the sample were not randomly sampled from all possible American bank managers; they were at a management training session. In order to generalize the results of the study to a larger population, we would need more information about the study and careful thinking about who the study participants might represent.





You have successfully completed this tutorial.

# [< Back to Section 4](https://bghammill.github.io/ims-04-foundations/)



<!-- MathJax -->

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

