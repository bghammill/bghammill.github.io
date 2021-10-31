




### Hypotheses

Let's revisit the High School and Beyond survey data. 

> Do the data provide convincing evidence of a difference between the average reading and writing scores of students? Use a 5% significance level.  
>
> $H_0: \mu_{\text{diff}} = 0$, There is no difference between the average reading and writing scores. 
>
> $H_A: \mu_{\text{diff}} \ne 0$, There is a difference between the average reading and writing scores.

Using the same High School and Beyond survey data from earlier, we answer the question "Do the data provide convincing evidence of a difference between the average reading and writing scores of students?" with a hypothesis test, at the 5% significance level.

The null hypothesis, representing the status quo of "there is nothing going on", says the average difference between the reading and writing scores of all students in the population is 0. In other words $\mu_{\text{diff}}$ is 0.

The alternative hypothesis, which follows from the research question, says that there is a difference. In other words $\mu_{\text{diff}}$ is different than 0.


### Testing for a mean with a t-test

```
hsb2 %>% 
  t_test(response = diff,
         null = 0, 
         alternative = "two-sided", 
         conf_int = FALSE)
```

We use the `t_test()` function again. The first argument is the variable of interest: the paired differences between reading and writing scores (`diff`). We then specify a `null` value for the hypothesis test, which is 0. We specify what direction should be used to calculate the p-value, based on the alternative hypothesis. Finally, since we are carrying out a hypothesis test, we can turn the confidence interval off (by setting it to `FALSE`). 


### Testing for a mean with a t-test

The resulting output states a p-value of 0.387.  With such a large p-value we fail to reject the null hypothesis and conclude that the data do not provide convincing evidence of a mean difference between the average reading and writing scores of students.



















## Confidence intervals for paired data

Next we discuss how we estimate the mean difference between data coming from two dependent groups, in other words, paired data. First, the good news: there's not much new here. We'll quickly see that we can summarize paired data in a way that allows us to re-use techniques we've already learned!

### High School and Beyond

As usual, let's frame our discussion around a real-world problem. 200 observations were randomly sampled from the High School and Beyond survey.

> The same students took a reading and writing test. 
> At a first glance, how are the distributions of reading and writing scores similar? How are they different?

![](C:\Users\bradh\Documents\GitHub\bghammill.github.io\ims-05-infer\ims-05-lesson-06\images\chp2-vid2-hsb2.png)

It appears that the median writing score is slightly higher than the median reading score. Both distributions seem fairly symmetric. But the reading scores are slightly more right skewed, as evidenced by the median that is closer to the 25th percentile than the 75th percentile. Also, the reading scores are slightly more variable than the writing scores. That all being said, at a first glance it's difficult to tell if there's a difference in the reading and writing scores.


### Independent scores?

> Can reading and writing scores for a given student student assumed to be independent of each other?
> Probably not! 

A student's reading score is likely not independent of their writing score. As both tests reflect students' knowledge, it is likely that they share information. 


### Analyzing paired data


> - When two sets of observations have this special correspondence (repeated observations), they are said to be paired.
> - To analyze paired data, it is often useful to look at the difference in outcomes of each pair of observations:
>   `diff = read − write`.

| student | read | write | diff |
| ------: | ---: | ----: | ---: |
|       1 |   57 |    52 |    5 |
|       2 |   68 |    59 |    9 |
|       3 |   44 |    33 |   11 |
|     ... |  ... |   ... |  ... |
|     200 |   63 |    65 |   -2 |


To analyze paired data, it is often useful to look at the difference in outcomes of each pair of observations. Here, for example, for each student we would subtract their writing score from their reading score to create a new variable, called `diff`, the difference between the two scores.

### Estimating the mean difference in paired data

> Construct a 95% confidence interval for the mean difference between the average reading and writing scores.

Our goal is to construct a 95% confidence interval for the true mean difference between the average reading and writing scores. 

Notice here we are talking about the "mean difference" rather than the "difference in means." That is not an accident, it is how we talk about the mean of paired data. Here, we are interested in the mean of the differences between reading and writing scores. This is different than taking a difference in the mean of the writing scores and the mean of the reading scores. 

Implementation-wise there is not much new here. Our data is in a singe column, `diff`, so we simply build an interval for the mean of this single variable using the `t_test()` function.

```
hsb2 %>% 
  t_test(response = diff,
         conf_int = TRUE, 
         conf_level = 0.95)
```

The 95% confidence interval is found to be (-1.78, 0.69).


### Interpreting the CI for mean difference in paired data

So how we interpret this 95% confidence interval for the mean difference between the reading and writing scores? 

A standard interpretation would say something along the lines of "95% confidence interval for the mean difference in reading and writing scores (read minus write) is -1.78 to 0.69.

But a good interpretation should convey directionality, in other words tell us which group is bigger than the other. 

> We are 95% confident that the true average reading score is between 1.78 points lower to 0.69 points higher than the average writing score. 



