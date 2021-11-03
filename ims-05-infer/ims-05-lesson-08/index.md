
# Comparing many means

So far, we discussed inference on a single mean as well as inference for comparing two means. Next we move on to comparing many means simultaneously.

Our motivating data comes from the General Social Survey. The two variables of interest are vocabulary score, `wordsum`, and self-identified social class, `gssclass`. We'll be assessing whether or not a respondent's vocabulary score is associated with their self-reported social class or not.

The vocabulary score is calculated based on the number of correct answers on a ten-question vocabulary test, where a higher score means better vocabulary, and self-identified social class has 4 levels: lower, working, middle, and upper class.

Let's load the data and set up the SAS environment:

```
* Initialize this SAS session;
%include "~/my_shared_file_links/hammi002/sasprog/run_first.sas";

* Load randomization macros;
%include "~/my_shared_file_links/hammi002/sasprog/load-randomization.sas";

* Makes a working copy of GSS_WORDS data and check;
%use_data(gss_words);
%glimpse(gss_words);
```

### Exploratory data analysis

Before we conduct inference, let's look at the distribution of each of these variables, alone and in combination.

```
* Exploratory data analysis for WORDSUM & GSSCLASS;
proc sgplot data=gss_words;
	histogram wordsum / binwidth=1;
run;

proc sgplot data=gss_words;
	vbar gssclass;
run;

proc sgplot data=gss_words;
	hbox wordsum / group=gssclass grouporder=ascending;
run;
```

The distribution of vocabulary scores is shown with a histogram. The scores range between 0 and 10. The distribution is centered around 6, and looks roughly symmetric. There is a bit of a left skew, but nothing overly dramatic. 

The distribution of social class is shown using a bar chart. The bulk of the respondents identified as working class or middle class, with many fewer in the outer categories.

And the distributions of vocabulary scores across the levels of (self-identified) social class are shown using a series of box plots. There do seem to be differences by class, with the vocabulary score increasing across the first three classes (lower to middle).


## ANOVA

In order to conduct inference for this situation (comparing more than two means), we need to use <u>AN</u>alysis <u>O</u>f <u>VA</u>riance, or ANOVA.

The research hypotheses for an ANOVA are as follows:

* $$H_0$$: The mean vocabulary score is the same across all social classes; $$\mu_{lower} = \mu_{working} = \mu_{middle} = \mu_{upper}$$
* $$H_A$$: The mean vocabulary score for __*at least one*__ social class differs from the others. 

Notice that the alternative hypothesis __is not__ that the scores for all of the social classes are different! The negation (opposite) of assuming every group is equal is assuming that at least one group is different.

The general idea with ANOVA is to partition the total variability of the outcome into different buckets. The total variability of vocabulary scores is just the variance in scores of all survey respondents, ignoring social class, and we can partition it into two sets:

- Variability that can be attributed to differences in social class, also known as **between-group** variability. This is really what we're interested in.
- Variability attributed to other factors within social class, also known as **within-group** variability. This variability is not what we are interested in and is somewhat of a nuisance factor. If everyone within a certain social class had the same vocabulary score, then we would have no within-group variability and we would be able to more easily compare the vocabulary scores across groups. However, this is almost never the case, and we need to account for the variability within the groups we are interested in. 

## Parametric ANOVA

To understand this partitioning, let's look at the parametric (mathematical method) output of an ANOVA model from SAS. This is

```
* ANOVA for WORDSUM by GSSCLASS;
proc anova;
	class gssclass;
	model wordsum = gssclass;
run;
```

The key output is this table, which is referred to as the ANOVA table:

![](images/sas-anova.png)

The first row (Model) reflects the between-group variability of the vocabulary score. The second row (Error) reflects the within-group variability of the vocabulary score. And the third row (Total) reflects total variability of the vocabulary score. We often refer to the first row as the "group", or "model" row, and the second row as the "error" row. 

The values in the Sum of Squares column are calculated similarly to how we calculate the variance, except that they are not scaled by the sample size.

* Row #1: Sum of squares between (SSB) = $$\Sigma n_j (\bar{X}_j - \bar{X})^2$$, where $$n_j$$ is the number of observations and $$\bar{X}_j$$ is the mean value within within group $$j$$ 
* Row #2: Sum of squares error (SSE) = $$\Sigma \Sigma (X - \bar{X}_j)^2$$
* Row #3: Sum of squares total (SST) = $$\Sigma \Sigma (X - \bar{X})^2$$

See how each of these is a sum of squared deviations?

The values in the Mean Square column *are* scaled, but not by sample size. They are scaled by the degrees of freedom listed for each column.

* Row #1: Mean square between (MSB) = $$SSB / df_{SSB}$$ 
* Row #2: Mean square error (MSE) = $$SSE / df_{SSE}$$
* Row #3: Total variance (not shown) = $$SST / df_{SST}$$

If the mean square value in the final row were listed, you would be able to see and confirm that it was the total variance in vocab scores within the sample.

So, they reflect various versions of the total squared deviation from the mean (whichMore specifically, this is calculated as the total squared deviation from the mean of the response variable.

The first two values measure the total variability attributed to (a) the variability in vocabulary scores explained by social class, and (b) the variability left unexplained by social class. The sum of these two values makes up sum of squares total, in the last row, which measures the total variability in the response variable, in this case this would be the total variability of the vocabulary scores. 



One statistic not presented on the ANOVA table that might be of interest is the percentage of the variability in vocabulary scores explained by the social class variable. We can find this as the ratio of the sum of squares for class divided by the total sum of squares.  

> - $SST = 236.5644 + 2869.8003 = 3106.365$ -- Measures the total variability in the response variable 
> - Percentage of explained variability = $\frac{236.5644}{3106.365} = 7.6\%$ 

In this case, 7.6% of the variability in vocabulary scores is explained by self-identified social class. This is the same as the $R^2$ value we would obtain if instead performed a linear regression, explaining vocabulary score with self-identified social class.


### F-distribution

ANOVA uses a test statistic $F$, which represents a standardized ratio of variability in the sample means relative to the variability within the groups. If $H_0$ is true and the model conditions are not violated, the statistic ($F$) follows an $F$-distribution with parameters $df_1 = groups -1$ and $df_2 = n - groups$. 

In the plot below, you see that the $F$-distribution is right skewed! There are no negative values on the $F$-distribution! Thus, for every hypothesis test, **only** the upper tail of the $F$-distribution is used to calculate the p-value.

Similar to the $t$-distribution, the $F$-distribution is defined by degrees of freedom. Except, now there are two different degrees of freedom, the degrees of freedom of the groups and the degrees of freedom of the residuals. 

These degrees of freedom are called the "numerator" and "denominator" degrees of freedom, since they correspond to the mean squares used in the calculation of the $F$-statistic. The "numerator" degrees of freedom is the number of groups you have minus 1 (here: 4 - 1). The "denominator" degrees of freedom is the total number of observations minus the number of groups (here: 795 - 4). 

A plot of the $F$-distribution used to calculate the p-value for this hypothesis test is shown below. 

```
values <- rf(100000, df1 = 3, df2 = 791) %>% 
  tibble()

obs_stat <- aov(wordsum ~ class, gss) %>%
  tidy() %>% 
  filter(term == "class") %>% 
    select(statistic) %>% 
  pull()

values %>% 
ggplot(aes(x = .)) + 
  geom_density() + 
  xlim(c(0, 25)) + 
  labs(x = "F-statistic", 
       y = "Density", 
       title = "F-distribution with 3 and 791 Degrees of Freedom")

```


### F-statistic

The $F$-statistic is calculated as the ratio between the “between” and “within” group variabilities, assuming all groups means were equal. Here when we talk about "variability" we are scaling the sum of squares for each group by its degrees of freedom. This gets us to the mean square values seen in the table. The ratio of these mean squares is how the $F$-statistic is calculated. 

The p-value is the area under the $F$-distribution beyond the observed $F$-statistic. We draw conclusions based on this p-value just like with any other hypothesis test we've seen so far.


```
aov(wordsum ~ class, gss) %>%
  tidy()
```

> F-statistic = $\frac{\frac{between~group~var}{group~size}}{\frac{within~group~var}{n - group~size}} = \frac{\frac{236.5644}{3}}{\frac{2869.8003}{791}} = \frac{78.854810}{3.628066} = 21.73467$

</br> 

The same $F$-distribution as above is shown below, with the observed $F$-statistic is displayed in red. 

```
values <- rf(100000, df1 = 3, df2 = 791) %>% 
  tibble()

obs_stat <- aov(wordsum ~ class, gss) %>%
  tidy() %>% 
  filter(term == "class") %>% 
    select(statistic) %>% 
  pull()

values %>% 
ggplot(aes(x = .)) + 
  geom_density() + 
  xlim(c(0, 25)) + 
  labs(x = "F-statistic", 
       y = "Density", 
       title = "F-distribution with 3 (numerator) and 791 (denominator) \n Degrees of Freedom") + 
  geom_vline(xintercept = obs_stat, color = "red", linetype = "dashed")

```

Time to put this into practice.

## Your turn! 

__ANOVA for evaluation score vs. rank of professor__

Let's conduct the ANOVA for evaluating whether there is a difference in the average evaluation score between the different ranks of professors at the University of Texas at Austin.

Use the `evals` data to perform an ANOVA for evaluation `score` based on professor `rank`. Use the following steps: 

- Use the `aov()` to perform the ANVOA, with inputs `score` and `rank`. 
- Store the resulting object as `aov_evals_rank`.
- View a `tidy()` output of this object.


```
# Run an analysis of variance on score vs. rank
aov_evals_rank <- aov(___, data = ___)

# Tidy the model
tidy(aov_evals_rank)
```

<div id="vocabulary_2-hint">
**Hint:** 
- Call `aov()`, passing a formula of `score ~ rank`, using the `evals` dataset.
- Call `tidy()`, passing the `aov_evals_rank` AOV model.
</div>


```
# Run an analysis of variance on score vs. rank
aov_evals_rank <- aov(score ~ rank, data = evals)

# Tidy the model
tidy(aov_evals_rank)

```

Interpret the result in context of the data and the research question. If needed, use a 2.5% significance level.  

Would your conclusion change if you had used a 10% significance level?


## Conditions for ANOVA

Just like any other statistical inference method we've encounter so far, there are mathematical conditions that need to be met for an ANOVA as well. Since we cannot mathematically "prove" that these conditions have been "met," we will use a careful eye to evaluate the degree to which each condition may be violated. 

There are three main conditions for ANOVA. The first one is independence. Within groups the sampled observations must be independent of each other, and between groups the groups must be independent of each other as well. 

We also need approximate normality, that is the distributions within each group should be nearly normal. 

Finally, we have the condition of constant variance. That is the variability of the distributions of the response variable within each group should have roughly the same variance.

> - **Independence:**
>     - within groups: sampled observations must be independent 
>     - between groups: the groups must be independent of each other
> - **Approximate normality:** distribution of the response variable should be nearly normal within each group
> - **Equal variance:** groups should have roughly equal variability

Next we'll discuss each condition in more detail.

### Independence

Let's start with the independence condition.

Within groups we want the samples observations to be independent. We can assume this is the case if we have random sampling. For experiments with random assignment but without random sampling, researchers would need to carefully consider if any of the observations could be related. For studies with small sample sizes, we need to be sure that the each sample size is less than 10% of its respective population. This condition is always important, but can be difficult to check if we don't have sufficient information on how the study was designed and data were collected.

Between groups we want the groups to be independent of each other. This requires carefully considering whether there is a paired structure between the groups. If the answer is yes, this is not the end of the world, but it requires a different, slightly more advanced version of ANOVA. 

> - **Within groups:** Sampled observations must be independent of each other
>     - Random sample 
>     - Each $n_j$ less than 10% of respective population always important, but sometimes difficult to check
>      
> - **Between groups:** Groups must be independent of each other  
>     - Carefully consider whether the groups may be dependent 
>     - Cannot have paired (or repeated) obervations in the groups


### Approximately normal

We also need the distribution of the response variable within each group to be approximately Normal. This condition is especially important when the sample sizes are small! We can check this condition using appropriate visualizations, which you'll get to do in the following exercises.

### Constant variance

Lastly we need constant variance across groups, in other words variability should be consistent across groups. This condition is especially important when the sample sizes differ between groups! We can use visualizations and/or summary statistics to check this condition.

Next we'll check the conditions for the vocabulary score vs. social class ANOVA that we have been working on.

### Checking the normality condition

```
question("Which of the following provides the most complete information for checking the normality condition for the ANOVA for evaluating whether there are differences between the average vocabulary scores across social classes?",
  correct = "Correct! A violin plot shows you the shape of the distribution.",
  allow_retry = TRUE,
  answer("Violin plot of vocabulary scores, faceted by social class", correct = TRUE),
  answer("Box plot of vocabulary scores, faceted by social class", message = "No. A box plot only gives you 5 metrics about the distribution of a variable, plus the positions of the outliers."),
  answer("Means and standard deviations of vocabulary scores in each social class", message = "No. This only gives you two metrics about the distribution of the scores, but doesn't tell you about the shape of the distribution."),
  answer("Number of modes of vocabulary scores in each social class", message = "No. This only gives you details of the modality of the distribution of the scores, but doesn't show you the shape of the distribution.")
)
```

### Checking the constant variance condition

In addition to checking the normality of distributions of vocabulary scores across levels of social class, we need to check that the variances from each are roughly constant.

In the exercise below, you'll calculate the standard deviations of each social class. To do this: 

1. `group_by()` social `class` 
2 `summarize()` each group with the standard deviations (`sd()` ) of vocabulary scores (`wordsum`)
3. Store these summaries in a column named `sd_wordsum`. 

Use these calculations to decide if it seems reasonable to assume that the groups have equal variability. 


```
gss %>%
  # Group by class
  group_by(___) %>%
  # Calculate the std dev of wordsum as std_dev_wordsum
  summarize(___ = ___)
```

<div id="vocabulary_3-hint">
**Hint:** 
- Call `group_by()`, passing `class` as the argument.
- Call `summarize()`, setting `std_dev_wordsum` to `sd(wordsum)`.
</div>

```
gss %>%
  # Group by class
  group_by(class) %>%
  # Calculate the std dev of wordsum as std_dev_wordsum
  summarize(sd_wordsum = sd(wordsum))
```

### 

So, what do you think? Is the equal variance condition violated? 

Let's see what these standard deviations *look* like. 

Create ridge plots of the distribution of `wordsum` across the different self-identified social `class`es.  

```
gss %>%
  # Map wordsum to the x-axis and class to the y-axis
  ggplot(aes(x = ___, y = ___)) +
  # Add density ridges to the plot! 
  geom_density_ridges()
```

<div id="vocabulary_4-hint">
**Hint:** 
- Call `ggplot()`, passing `wordsum` to the `x` variable and `class` to the `y` variable. 
</div>

```
gss %>%
  # Map wordsum to the x-axis and class to the y-axis
  ggplot(aes(x = wordsum, y = class)) +
  # Add density ridges to the plot! 
  geom_density_ridges()
```

So, what do you think now? Is the equal variance condition violated? 

## Simulation-based ANOVA

If the condition of normality is violated, then the $F$-distribution is a poor approximation for what the true sampling distribution of the F-statistics looks like. Instead, we should use a more robust method. The familiar randomization and bootstrapping methods from before can be extended into the ANOVA framework. We really only need to make one modification:

> When we shuffle our observations together and make new groups, now we will have more than two groups to divide the observations into. 
All of the other steps for creating the null distribution are the same! Let's see how this is done. 

### Calculating the observed statistic

In the case of an ANOVA, the statistic we are interested in is the F-statistic. While we can plot this statistic on an $F$-distribution, is really is just another statistic that we can calculate (like the mean or median). We like this statistic because it allows for us to summarize how different multiple means are from each other, relative to how variable the observations are within each group. 

We can `calculate` the F-statistic using the tools from the infer package we are familiar with. The only part that is new is the `stat` that we calculate. Here, we use the `"F"` statistic. This is what this looks like: 

```
obs_stat <- gss %>% 
  specify(wordsum ~ class) %>% 
  calculate(stat = "F")
```

The observed statistic for these data is an F-statistic equal to `r pull(obs_stat)`. Let's see how extreme this is, if there really is no difference in the `wordsum` between the four groups. 

### Simulating samples under the null hypothesis

The next step is to simulate what we would expect for `wordsum`s to look like, if the null hypothesis was true. This is similar to the method for a difference in means, except now we have four groups: lower, middle, upper, and working. The underlying process, however, looks the same:

- Step 1: Write the values of `wordsum` on 795 index cards (one card per person).
- Step 2: Shuffle the cards and randomly split them into four new piles, of the same size as the original groups.
- Step 3: Calculate and record the test statistic: F-statistic
- Step 4: Repeat steps (1) and (2) many times to generate the sampling distribution of the difference in means under the null hypothesis.
- Step 5: Calculate p-value as the percentage of simulations where the test statistic is at least as extreme as the observed F-statistic

###

Let's start with steps 1-4. Using the code below, fill in the missing pieces necessary to simulate F-statistics that could have happened if the null hypothesis was true. 

Once you've filled in the code, take a look at what the F-statistics stored inside of `null_dist` look like!

```
null_dist <- gss %>% 
  specify(___ ~ ___) %>% 
  hypothesize(null = "___") %>% 
  generate(reps = 1000, type = "permute") %>% 
  calculate(stat = "___")

```

```
specify(wordsum ~ class) 
```

```
hypothesise(null = "independence")
```

```
calculate(stat = "F")
```

###

Now that we have our observed F-statistic and our null distribution, we can calculate our p-value! Let's visualize where the observed statistic lies on the null distribution first. 

```
null_dist <- gss %>% 
  specify(wordsum ~ class) %>% 
  hypothesize(null = "independence") %>% 
  generate(reps = 1000, type = "permute") %>% 
  calculate(stat = "F")
```

```
null_dist %>% 
  visualise() +
  shade_p_value(obs_stat = obs_stat, direction = "greater")
```

Woah! That's a long ways away from the null distribution! As you would suspect, there are no permuted samples with F-statistics as or more extreme that what we observed in the dataset. Thus, our p-value is approximately 0. 

Similar to before, with this p-value we can conclude that *at least one* of the classes has a different mean `wordsum`. But, you might wonder, which of the classes are different? That's where post-hoc testing picks up! 


## Post-hoc testing

So far we've introduced ANOVA as a method for comparing many means to each other concurrently. Finding a statistically significant result at the end of an ANOVA however only tells us that at least one pair of means are different, but not which pair of means are different. Next, we set out to answer this follow up question.

### Which means differ?

What we're talking about is performing lots and lots of $t$-test, testing which of the groups are different. Unfortunately, performing lots and lots of hypothesis test has some major downsides. The main one we are worried about is the inflation in the Type I error rate. 
If you remember back to the [Errors in hypothesis testing tutorial](https://openintro.shinyapps.io/ims-05-introduction-to-statistical-inference-03), a Type I error is when we reject the null hypothesis when it is true. We specify a threshold for the percentage of times we are willing to make this type of error by selecting an $\alpha$. So, an $\alpha$ of 0.05, says we are willing to make a Type I error 5% of the time. 

If we think about performing multiple $t$-tests all at a 5% significance level, then our error rate begins to grow. Specifically, if we perform 10 tests all with an $\alpha$ of 0.05, the probability of not making a Type I error for each test is $(1- \alpha)$. If we then compute the overall probability of not making a Type I error we'd have $1 - (1- \alpha)^n \approx n \cdot \alpha.$ For 10 tests at a $\alpha$ of 0.05, the probability of not making a Type I error is approximately 50%. That's not very unlikely!  

So, what we're interested in is controlling the Type I error rate, when performing many pairwise tests in the quest for identifying the groups whose means are "significantly" different from each other.

Fortunately, there is a simple solution to this problem: use a modified significance level. 

That is, we'll use a "family" error rate, and then distribute that level to each of the tests we are performing. The "family" error rate specifies an overall Type I error rate we are willing to have for **all** of tests you wish to perform. 


### Multiple comparisons

Testing many pairs of groups is called multiple comparisons. 

A common modification we use when doing multiple comparisons is the Bonferroni correction. This correction fixes a "family" error rate which then transfers to a more stringent significance level for each of the pairwise tests. 

More specifically, we will adjust our "family" $\alpha$ by the number of comparisons we are doing. 

The Bonferroni corrected significance level can be calculated as the original significance level ($\alpha$) divided by the number of pairwise comparisons to be carried out. 

If we think back to our days in Algebra, we can calculate the total number of tests using combinatorics. Specifically, for $k$ groups we will have ${k \choose 2}$ possible pairwise tests. This number of tests can be calculated as $\frac{k \cdot (k - 1)}{2}$, where k is the number of groups in the ANOVA.

> - Testing many different pairs of groups is called multiple comparisons
> - Due to the inflated Type I error rate, a correction for the significance level is needed 
>     - The Bonferroni correction uses a more stringent significance level  
>     - It adjusts $\alpha$ by the number of comparisons being considered 
>     - The new $\alpha$ is $\alpha^\star = \frac{\alpha}{K}$, where $K = \frac{k (k-1)}{2}$ 



Now it's your turn.

### Calculate $\alpha^*$

Which of the following is the correct modified significance value for the post hoc tests associated with ANOVA for evaluating which of the self-identified social classes have different average vocabulary scores?

There are 4 social classes, and the original significance level was 5%.

*Hint:* For $k$ groups, there are $k \cdot (k - 1) / 2$ pairwise comparisons.

```
question("",
  correct = "Bonferroni would be so proud! The correction factor for $k$ classes is $k * (k - 1) / 2$.",
  allow_retry = TRUE,
  answer("0.05", message = "No. You need to correct for the multiple comparisons of pairs of social classes."),
  answer("0.05 / 4", message = "No. You should correct for *pairwise* comparisons of social classes."),
  answer("0.05 / 6", correct = TRUE),
  answer("0.05 / 12", message = "No. Comparing class `A` to class `B` is the same as comparing class `B` to class `A`, so you need to halve the correction factor.")
)
```

### Compare pairwise means

We can compute all of the pairwise $t$-tests for our 4 social classes using the `pairwise.t.test()` function. This function is a bit different than other functions we have used, in that it expects for the variables to be extracted from the dataframe when they are input. This is different from our "usual" workflow where we "pipe" data from the dataset into the function. 

There are two ways to handle this,  

1. Extract the columns you are interested in using a `$` and use as the inputs to the `pairwise.t.test()` function.  
2. Use a modified pipe operator to extract the columns (`%$%`), when piping the data into the `pairwise.t.test()` function. 

We will present the modified pipe operator option, but both methods will be displayed in the solution. 

Additionally, the `pairwise.t.test()` function takes three main inputs,   

1. the response variable (input as `x`)
2. the grouping variable (input as `g`)
3. the adjustment method to use (input as `p.adjust.method`) -- there are other adjustment methods, but in this exercise we will focus on the `"bonferroni"` adjustment 

### 

For this exercise, you are to conduct a pairwise t-test on vocabulary scores and social class.

- Use the `pairwise.t.test()` function to obtain the pairwise $t$-tests. 
- Set the `p.adjust.method` to `"Bonferroni"` to obtain the Bonferroni corrected p-values.
- Tidy the resulting table. 
- Compare the adjusted p-values to the un-adjusted p-values. Which p-values are smaller? 


```
# Run a pairwise t-test on wordsum and class, without adjustment
t_test_results <- gss %$%  
  pairwise.t.test(x = ___, g = ___, p.adjust.method = "___")


# Tidy the result
tidy(___)

## Compare with un-adjusted pairwise t-tests
gss %$% 
  pairwise.t.test(wordsum, class, p.adjust.method = "none")
```

<div id="vocabulary_4-hint">
**Hint:** 
- Use `wordsum` as `x`, `class` as `g`, and set `p.adjust.method` to `"bonferroni"`.
- Call `tidy()`, passing `t_test_results`.
</div>

```
# Run a pairwise t-test on wordsum and class, without adjustment
t_test_results <- gss %$%  
  pairwise.t.test(x = wordsum, g = class, p.adjust.method = "bonferroni")

## Alternative method 
t_test_results <- pairwise.t.test(gss$wordsum, gss$class, p.adjust.method = "bonferroni")

# Tidy the result
tidy(___)

## Compare with un-adjusted pairwise t-tests
gss %$% 
  pairwise.t.test(wordsum, class, p.adjust.method = "none") %>% 
  tidy()

```

### 


Do the data provide convincing evidence of a difference in the average vocabulary scores of those who self-identified as middle class and those who self-identified as lower class?

For which of the pairwise comparisons would you conclude that there is a "significant" difference between the group means?

## Congratulations!

You have successfully completed Lesson 8 in Tutorial 5: Statistical Inference.   

You should now have a very good understanding of statistical inference for numerical data. In this tutorial you have learned about parametric procedures for conducing an analysis of variance (ANOVA). Specifically, you should now be familiar with the concepts of variability within and between groups, the $F$-distribution, and multiple comparison procedures. 

What's next?

[Full list of tutorials supporting OpenIntro::Introduction to Modern Statistics](https://bghammill.github.io/)

[Tutorial 5: Statistical Inference](https://bghammill.github.io/ims-05-infer/)

- [Tutorial 5 - Lesson 1: Inference for a single proportion](https://bghammill.github.io/ims-05-infer/ims-05-lesson-01/)
- [Tutorial 5 - Lesson 2: Hypothesis tests to compare proportions](https://bghammill.github.io/ims-05-infer/ims-05-lesson-02/)
- [Tutorial 5 - Lesson 3: Chi-square test of independence](https://bghammill.github.io/ims-05-infer/ims-05-lesson-03/)
- [Tutorial 5 - Lesson 4: Chi-square test for goodness of fit](https://bghammill.github.io/ims-05-infer/ims-05-lesson-04/)
- [Tutorial 5 - Lesson 5: Bootstrapping to estimate a parameter](https://bghammill.github.io/ims-05-infer/ims-05-lesson-05/)
- [Tutorial 5 - Lesson 6: T-distribution](https://bghammill.github.io/ims-05-infer/ims-05-lesson-06/)
- [Tutorial 5 - Lesson 7: Inference for difference in two means](https://bghammill.github.io/ims-05-infer/ims-05-lesson-07/)
- [Tutorial 5 - Lesson 8: Comparing many means](https://bghammill.github.io/ims-05-infer/ims-05-lesson-08/)

[Learn more at Introduction to Modern Statistics](http://openintro-ims.netlify.app/)

<!-- MathJax -->

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

