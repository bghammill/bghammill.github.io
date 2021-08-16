## Observational studies and experiments

In this lesson, we discuss two types of studies commonly used in scientific research: observational studies and experiments. In particular, we will outline how to identify them and what the type of study says about the scope of the inference we can make based on that study's results.

In an *observational study*, researchers collect data in a way that does not directly interfere with how the data arise, in other words, they merely "observe". And based on an observational study we can only establish an association between the *explanatory* and *response* variables.

In an *experiment*, on the other hand, researchers randomly assign subjects to various treatments and can therefore establish causal connections between the *explanatory* and *response* variables.

Suppose we want to evaluate the relationship between using screens at bedtime such as a computer, tablet, or phone and attention span during the day. We can design this study as an observational study or as an experiment.

In an observational study, we sample two types of people from the population: those who choose to use screens at bedtime and those who don't.

Then, we find the average attention span for the two groups of people and compare.

![](images/design-observational.png)

On the other hand, in an experiment, we sample a group of people from the population and then we randomly assign these people into two groups: those who are asked to use screens at bedtime and those who asked not to use them. The difference is that the decision of whether to use screens or not is not left up to the subjects, as it was in the observational study, but is instead imposed by the researcher.

At the end, we compare the attention spans of the two groups.

![](images/design-experiment.png)

Based on the observational study, even if we find a difference between the average attention span of these two groups of people, we can't attribute this difference solely to using screens because there may be other variables that we didn't control for in this study that contribute to the observed difference. For example, people who use screens at night might also be using screens for longer time periods during the day and their attention span might be affected by the daytime usage as well.

However, in the experiment, such variables that might also contribute to the outcome, called confounding variables, are most likely represented equally in the two groups due to random assignment. Therefore, if we find a difference between the two averages, we can indeed make a causal statement attributing this difference to bedtime screen usage.

Let's put these ideas into practice.

### Identify type of study: Reading speed and font

A study is designed to evaluate whether people read text faster in Arial or Helvetica font. A group of volunteers who agreed to be a part of the study are randomly assigned to two groups: one where they read some text in Arial, and another where they read the same text in Helvetica. At the end, average reading speeds from the two groups are compared. 

*What type of study is this? An observational study, an experiment, or neither? Why did you answer that way?*

Next, let's take a look at data from a different study on country characteristics. First, load the data and view it, then identify the type of study. Remember, an experiment requires random assignment.

### Recap: Types of studies

Let's load and take a look at data from a different study on country characteristics. The data can be found in the **gapminder** dataset. 

```
* Initialize things if you have not done this already during this SAS session;
%include "~/my_shared_file_links/hammi002/sasprog/run_first.sas";

* Makes a working copy of GAPMINDER data and glimpse;
%use_data(gapminder);
%glimpse(gapminder);
```

*What type of study do you think these data came from? An observational study, an experiment, or neither? Why did you answer that way?*

## Random sampling assignment

In this section, we'll clarify the difference between *random sampling* and *random assignment*, and more importantly, why we care about this difference.

**Random sampling** occurs when _subjects are being selected for a study_. If subjects are selected randomly from the population of interest, then the resulting sample is likely representative of that population and therefore the study's results can be generalizable to that population.

**Random assignment** occurs only _in experimental settings where subjects are being assigned to various treatments_. Random assignment _allows for causal conclusions_ about the relationship between the treatment and the response. 

Here is a quick summary of how random sampling and random assignment affect the scope of inference of a study's results.

#### Scope of inference

![](images/scope-inference.png)

A study that employs random sampling and random assignment can be used to make causal conclusions and these conclusions can be generalized to the whole population. This would be an ideal experiment, but such studies are usually difficult to carry out, especially if the experimental units are humans, since it may be difficult to randomly sample people from the population and then impose treatments on them. This is why most experiments recruit volunteer subjects. You may have seen ads for these on a university campus or in a newspaper.

Such human experiments that rely on volunteers employ random assignment, but not random sampling. These studies can be used to make causal conclusions, but the conclusions only apply to the sample and the results cannot be generalized to the population.

A study that uses no random assignment, but does use random sampling is your typical observational study. Results can only be used to make association statements, but they can be generalized to the whole population.

A final type of study, one that doesn't use random assignment or random sampling, can only be used to make non causal association statements. This is an unideal observational study.

You'll get some more practice with these concepts now.

### Random sampling and random assignment?

One of the early studies linking smoking and lung cancer compared patients who are already hospitalized with lung cancer to similar patients without lung cancer (hospitalized for other reasons), and recorded whether each patient smoked. Then, proportions of smokers for patients with and without lung cancer were compared.

*Did this study employ random sampling and/or random assignment?*

No. Neither.  Random assignment is not employed because the conditions are not imposed on the patients by the people conducting the study; random sampling is not employed because the study records the patients who are already hospitalized, so it wouldn't be appropriate to apply the findings back to the population as a whole.

### Identify the scope of inference of study

Volunteers were recruited to participate in a study where they were asked to type 40 bits of trivia—for example, "an ostrich’s eye is bigger than its brain"—into a computer. A randomly selected half of these subjects were told the information would be saved in the computer; the other half were told the items they typed would be erased.

Then, the subjects were asked to remember these bits of trivia, and the number of bits of trivia each subject could correctly recall were recorded. It was found that the subjects were significantly more likely to remember information if they thought they would not be able to find it later.

*Can the results of this study be generalized to all people?*

No. There is no random sampling since the subjects of the study were volunteers, so the results cannot be generalized to all people.

*Do the results of this study infer a causal link between believing information is stored and memory?*

Yes, due to random assignment, we are able to infer a causal link between the belief information is stored and the ability to recall that same information.

## Simpson's paradox

Often when one mentions "a relationship between variables" we think of a relationship between just two variables, say a so called explanatory variable, $$x$$, and response variable, $$y$$. However, truly understanding the relationship between two variables might require considering other potentially related variables as well. If we don't, we might find ourselves in a *Simpson's paradox*. So, what is Simpson's paradox?

First, let's clarify what we mean when we say explanatory and response variables. Labeling variables as explanatory and response does not guarantee the relationship between the two is actually causal, even if there is an association identified. We use these labels only to keep track of which variable we suspect affects the other.

### Explanatory and response

![](images/explanatory-single.png)

And these definitions can be expanded to more than just two variables. For example, we could study the relationship between three explanatory variables and a single response variable.

### Multivariate relationships

![](images/explanatory-multivariate.png)

This is often a more realistic scenario since most real world relationships are multivariable. For example, if we're interested in the relationship between calories consumed daily and heart health, we would probably also want to consider information on variables like age and fitness level of the person as well.

![](images/explanatory-multivariate-example.png)

Not considering an important variable when studying a relationship can result in what we call a **Simpson's paradox**. This paradox illustrates the effect the omission of an explanatory variable can have on the measure of association between another explanatory variable and the response variable. In other words, the inclusion of a third variable in the analysis can change the apparent relationship between the other two variables. 

Consider the eight dots in the scatter plot below (the points happen to fall on the orange and blue lines). The trend describing the points when only considering `x1` and `y`, illustrated by the black dashed line, is reversed when `x2`, the grouping variable, is also considered. If we don't consider `x2`, the relationship between `x1` and `y` is positive. If we do consider `x2`, we see that within each group the relationship between `x1` and `y` is actually negative.

![](images/simpson-paradox-plot.png)

We'll explore Simpson's paradox further with another dataset, which comes from a study carried out by the graduate Division of the University of California, Berkeley in the early 70’s to evaluate whether there was a sex bias in graduate admissions. The data come from six departments. For confidentiality we'll call them A through F. The dataset contains information on whether the applicant identified as male or female, recorded as `Gender`, and whether they were admitted or rejected, recorded as `Admit`. First, we will evaluate whether the percentage of males admitted is indeed higher than females, overall. Next, we will calculate the same percentage for each individual department.

### Berkeley admission data

|        | Admitted | Rejected |
| ------ | -------- | -------- |
| Male   | 1198     | 1493     |
| Female | 557      | 1278     |

> Note: At the time of this study, gender and sexual identities were not given distinct names. Instead, it
> was common for a survey to ask for your "gender" and then provide you with the options of "male" and "female." Today, we better understand how an individual's gender and sexual identities are different pieces of who they are. To learn more about inclusive language surrounding gender and sexual identities see the [gender unicorn](https://transstudent.org/gender/). 

Let's get started.

### Number of males and females admitted

The goal of this exercise is to determine the numbers and proportions of male and female applicants who got admitted and rejected. Specifically, we want to find out how many males are admitted and how many are rejected. And similarly we want to find how many females are admitted and how many are rejected.

Let's first load and check the **ucb_admit** dataset:

```
* Makes a working copy of UCB_ADMIT data and glimpse;
%use_data(ucb_admit);
%glimpse(ucb_admit);
```

To count admissions and see admission rates by group, we will use `PROC FREQ`:

```
* Count admissions by male/female;
proc freq data=ucb_admit;
	tables gender * admitted / nocol nopct;
run;
```

The `nocol` and `nopct` options above suppress the column and overall percentages from being shown, so we are left with just the counts and the row percentages (i.e., % of each group that is admitted). This is what we're after since the first variable (`gender`) in a 2-variable tables statement defines the row variable, while the second variable (`admitted`) defines the column variable.

Based on the resulting table, answer the following question: *Which gender had a higher admission rate, male or female?*

### Proportion of males admitted for each department

Finally we'll make a table similar to the one we constructed earlier, except we'll first group the data by department. The goal is to compare the proportions of male students admitted across departments.

We can do this easily by adding a third variable in the `PROC FREQ` `tables` statement, prior to the variable that are there already, as:

```
* Count admissions by male/female, by department;
proc freq data=ucb_admit;
	tables dept * gender * admitted / nocol nopct;
run;
```

Now the percentages shown reflect the male/female-specific admission rates separately **for each department**.

Based on these results, *which of the following statements best describes the relationship between admission status and gender*?

1. Within most departments, female applicants are more likely to be admitted.
2. Within most departments, male applicants are more likely to be admitted.
3. Within most departments, male and female applicants are equally likely to be admitted.

Surprising given the overall results, no?

### Recap: Simpson's paradox


We'll wrap up the lesson with a recap of our findings.

Overall, males were more likely to be admitted. Within most department, however, females were more likely to be admitted. Meaning, when controlling for department, the relationship between gender and admission status was reversed. This is Simpson's paradox.

We would have to know about the data to tease out the reasons for this, but one potential explanation for this paradox is that women tended to apply to competitive departments with lower rates of admission even among qualified applicants, such as in the English Department. Whereas, men tended to apply to less competitive departments with higher rates of admission among the qualified applicants, such as in engineering and chemistry.

Note that we were only able to discover the contradictory finding once we incorporated information about the department of the application. Examples like this highlight the importance of a good study design that considers and collects information on extraneous, but potentially confounding variables in a study.

## Congratulations!

You have successfully completed Lesson 2 in Tutorial 1: Getting Started with Data.

What's next?

[Full list of tutorials supporting OpenIntro::Introduction to Modern Statistics](https://bghammill.github.io/)

[Tutorial 1: Getting Started with Data](https://bghammill.github.io/ims-01-data/)

- [Tutorial 1 - Lesson 1: Language of data](https://bghammill.github.io/ims-01-data/ims-01-lesson-01/)
- [Tutorial 1 - Lesson 2: Types of studies](https://bghammill.github.io/ims-01-data/ims-01-lesson-02/)
- [Tutorial 1 - Lesson 3: Sampling strategies and Experimental design](https://bghammill.github.io/ims-01-data/ims-01-lesson-03/)
- [Tutorial 1 - Lesson 4: Case study](https://bghammill.github.io/ims-01-data/ims-01-lesson-04/)

[Learn more at Introduction to Modern Statistics](http://openintro-ims.netlify.app/)

<!-- MathJax -->

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

