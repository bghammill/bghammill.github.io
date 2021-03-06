## Emails

In this lesson, you'll get a chance to put to use what you know about exploratory data analysis in exploring a new dataset. 

The `email` dataset contains information on all of the emails received by a single email account in a single month. Each email is a case, so we see that this email account received 3,921 emails. Twenty-one different variables were recorded on each email, some of which are numerical, others are categorical. Of primary interest is the first column, a categorical variable indicating whether or not the email is `spam`. This was created by actually reading through each email individually and deciding if it looked like spam or not. The subsequent columns were easier to create. `to_multiple` is TRUE if the email was addressed to more than one recipient and FALSE otherwise. `image` is the number of images attached in the email. It's important that you have a full sense of what each of the variables mean, so you'll want to start your exercises by reading about them in the [data set description](https://www.openintro.org/data/index.php?data=email).

```
email
```

One of your guiding questions throughout this lesson is: what characteristics of an email are associated with it being spam? Numerical and graphical summaries are a great way of starting to answer that question.

### Spam and num_char

Is there an association between spam and the length of an email? You could imagine a story either way: 

* *Spam is more likely to be a short message tempting me to click on a link*, or
* *My normal email is likely shorter since I exchange brief emails with my friends all the time*.

Here, you'll use the `email` dataset to settle that question.

As you explore the association between spam and the length of an email, we'll use the **tidyverse** package for building data pipelines and visualisations and the **openintro** package that contains the `email`. These are loaded for you below.

```
library(tidyverse)
library(openintro)
```

Using the `email` dataset:

- Compute appropriate measures of the center and spread of `num_char` for both spam and not spam using `group_by()` and `summarize()`. No need to name the new columns created by `summarize()`.
- Construct side-by-side box plots to visualize the association between the same two variables. It will be useful to `mutate()` a new column containing a log-transformed version of `num_char`.
```
# Compute summary statistics
email %>%
  ___ %>%
  ___
# Create plot
email %>%
  mutate(log_num_char = ___) %>%
  ggplot(aes(x = ___, y = log_num_char)) +
  ___
```

```
# Compute summary statistics
email %>%
  group_by(spam) %>%
  ___
# Create plot
email %>%
  mutate(log_num_char = ___) %>%
  ggplot(aes(x = ___, y = log_num_char)) +
  ___
```

```
# Compute summary statistics
email %>%
  group_by(spam) %>%
  summarize(median(num_char),
            IQR(num_char))
# Create plot
email %>%
  mutate(log_num_char = ___) %>%
  ggplot(aes(x = ___, y = log_num_char)) +
  ___
```

```
# Compute summary statistics
email %>%
  group_by(spam) %>%
  summarize(median(num_char),
            IQR(num_char))
# Create plot
email %>%
  mutate(log_num_char = log(___)) %>%
  ggplot(aes(x = ___, y = log_num_char)) +
  ___
```

```
# Compute summary statistics
email %>%
  group_by(spam) %>%
  summarize(median(num_char),
            IQR(num_char))
# Create plot
email %>%
  mutate(log_num_char = log(num_char)) %>%
  ggplot(aes(x = spam, y = log_num_char)) +
  geom_boxplot()
```

### Spam and num_char interpretation

```
email %>%
  mutate(log_num_char = log(num_char)) %>%
  ggplot(aes(x = spam, y = log_num_char)) +
  geom_boxplot()
```

```
question("Which of the following interpretations of the plot is valid?",
  answer("The shortest email in this dataset was not spam.", message = "Nope, try again."),
  answer("The median length of not spam emails is greater than that of spam emails.", correct = TRUE, message = "Awesome!"),
  answer("The IQR of email length of spam is greater than that of not spam.", message =),
  allow_retry = TRUE,
  incorrect = "The greater the log-transformed `num_char` value is, the longer the email."
)
```

### Spam and !!!

Let's look at a more obvious indicator of spam: exclamation marks. `exclaim_mess` contains the number of exclamation marks in each message. Using summary statistics and visualization, see if there is a relationship between this variable and whether or not a message is spam.

Experiment with different types of plots until you find one that is the most informative. Recall that you've seen:

- Side-by-side box plots
- Faceted histograms
- Overlaid density plots

The `email` dataset is still available in your workspace.

- Calculate appropriate measures of the center and spread of `exclaim_mess` for both spam and not spam using `group_by()` and `summarize()`.
- Construct an appropriate plot to visualize the association between the same two variables, adding in a log-transformation step if necessary.
- If you decide to use a log transformation, remember that `log(0)` is `-Inf` in R, which isn't a very useful value! You can get around this by adding a small number (like `0.01`) to the quantity inside the `log()` function. This way, your value is never zero. This small shift to the right won't affect your results.

**Hint:** Take a look at how we calculated the summary statistics for `num_char` with the `median()` and `IQR()` functions in the `summarize()` call earlier.

```
# Compute center and spread for exclaim_mess by spam
# Create plot for spam and exclaim_mess
```




```
# Compute center and spread for exclaim_mess by spam
email %>%
  group_by(spam) %>%
  summarize(median(exclaim_mess),
            IQR(exclaim_mess))
# Create plot for spam and exclaim_mess
email %>%
  mutate(log_exclaim_mess = log(exclaim_mess + 0.01)) %>%
  ggplot(aes(x = log_exclaim_mess)) +
  geom_histogram() +
  facet_wrap(~ spam)
# Alternative plot: side-by-side box plots
email %>%
  mutate(log_exclaim_mess = log(exclaim_mess + 0.01)) %>%
  ggplot(aes(x = 1, y = log_exclaim_mess)) +
  geom_boxplot() +
  facet_wrap(~ spam)
# Alternative plot: Overlaid density plots
email %>%
  mutate(log_exclaim_mess = log(exclaim_mess + .01)) %>%
  ggplot(aes(x = log_exclaim_mess, fill = spam)) +
  geom_density(alpha = 0.3)
```

### Spam and !!! interpretation

```
email %>%
  mutate(log_exclaim_mess = log(exclaim_mess + .01)) %>%
  ggplot(aes(x = log_exclaim_mess)) +
  geom_histogram() +
  facet_wrap(~spam)
```

```
question(
  "Which interpretation of these faceted histograms **is not** correct?
",
  answer("The most common value of `exclaim_mess` in both classes of email is zero (a `log(exclaim_mess)` of -4.6 after adding .01)."),
  answer("There are more cases of spam in this dataset than not spam.", correct = TRUE, message = "Well done!"),
  answer("Even after a transformation, the distribution of `exclaim_mess` in both classes of email is right-skewed."),
  answer("The typical number of exclamations in the not spam group appears to be slightly higher than in the spam group."),
  allow_retry = TRUE,
  incorrect = "There are fewer cases of spam in this dataset than not spam."
)
```

## Check-in 1

Let's review what you learned in the last several exercises. 

The box plots show the association between whether or not an email is spam and the length of the email, as measured by the log number of characters. In this dataset, the typical spam message is considerably shorter than the non-spam message, but there is still a reasonable amount of overlap in the two distributions of length.

When you looked at the distribution of spam and the number of exclamation marks used, you found that both distributions are heavily right skewed: there only a few instances of many exclamation marks being using and many many more of 0 or 1 being used. It also bucks the expectation that spam messages will be filled with emphatic exclamation marks to entice you to click on their links. If anything, here it's actually not spam that typically has more exclamation marks.

The dominant feature of the exclaim mess variable, though, is the large proportion of cases that report zero or on this log scale, -4.6 exclamation marks. This is a common occurrence in data analysis that is often termed "zero-inflation", and there are several common ways to think about those zeros.

```
p1 <- email %>% 
  mutate(log_num_char = log(num_char)) %>% 
  ggplot(aes(y = spam , x = log_num_char)) +
  geom_boxplot()
p2 <- email %>% 
  mutate(log_exclaim_mess = log(exclaim_mess+0.01)) %>% 
  ggplot(aes(x = log_exclaim_mess)) +
  geom_histogram()+
  facet_wrap(~spam)
p1 / p2
```

### Zero inflation strategies

One approach says that there are two mechanisms going on: one generating the zeros and the other generating the non-zeros, so we will analyze these two groups separately. A simpler approach is to think of the variable as two level categorical variable (zero or not zero) and to treat it like a categorical variable.

```
email2 <- email %>%
  mutate(zero = exclaim_mess == 1, log_exclaim_mess = log(exclaim_mess))
email2 %>% 
  ggplot(aes(x = log_exclaim_mess, fill = zero)) +
  geom_histogram()+
  facet_wrap(~spam)+
  theme(legend.position = "none") 
```

### 

To do so we create a new variable, we'll call it `zero` and use it to color in our bar chart.

```
email %>%
  mutate(zero = exclaim_mess == 0) %>%
  ggplot(aes(x = zero, fill = zero)) +
  geom_bar() +
  facet_wrap(~spam)
```

In the resulting plot, yes, we've lost a lot of information. But it's become very clear that spam is more likely to contain no exclamation marks, while in regular mail (not spam mail), the opposite is true.

### Bar plot options

Another way that we've seen is using a stacked bar chart. For that plot, you move the second variable from the facet layer to the fill argument inside the aesthetics function. 

```
email %>%
  mutate(zero = exclaim_mess == 0) %>%
  ggplot(aes(x = zero, fill = spam)) +
  geom_bar()
```

The other consideration you have to make is if you're more interested in counts or proportions. If the latter, you'll want to normalize the plot, which you can do by adding the position equals fill argument to `geom_bar()`. The result is a series of conditional proportions, where you're conditioning on whichever variable you're in.

```
email %>%
  mutate(zero = exclaim_mess == 0) %>%
  ggplot(aes(x = zero, fill = spam)) +
  geom_bar(position = "fill")
```

### Collapsing levels

If it was difficult to work with the heavy skew of `exclaim_mess`, the number of images attached to each email (`image`) poses even more of a challenge. Run the following code to get a sense of its distribution: 

```
email %>%
  count(image)
```

Recall that this tabulates the number of cases in each category (so there were 3811 emails with 0 images, for example). Given the very low counts at the higher number of images, let's collapse `image` into a categorical variable that indicates whether or not the email had at least one image. In this exercise, you'll create this new variable and explore its association with spam.

Starting with `email`, form a continuous chain that links together the following tasks:

- Create a new variable called `has_image` that is `TRUE` where the number of images is greater than zero and `FALSE` otherwise.
- Create an appropriate plot with `email` to visualize the relationship between `has_image` and `spam`.

```
# Create plot of proportion of spam by image
email %>%
  mutate(has_image = ___) %>%
  ggplot(aes(x = ___, fill = ___)) +
  geom_bar(position = ___)
```

```
email %>%
  mutate(has_image = image > 0) %>%
  ggplot(aes(x = ___, fill = ___)) +
  geom_bar(position = ___)
```

```
email %>%
  mutate(has_image = image > 0) %>%
  ggplot(aes(x = ___, fill = ___)) +
  geom_bar(position = "fill")
```

```
# Create plot of proportion of spam by image
email %>%
  mutate(has_image = image > 0) %>%
  ggplot(aes(x = has_image, fill = spam)) +
  geom_bar(position = "fill")
```

### Image and spam interpretation

```
email %>%
  mutate(has_image = image > 0) %>%
  ggplot(aes(x = has_image, fill = spam)) +
  geom_bar(position = "fill")
```

```
question(
  "Which of the following interpretations of the plot **is** valid?",
  answer("There are more emails with images than without images."),
  answer("Emails with images have a higher proportion that are spam than do emails without images.", correct = TRUE),
  answer("An email without an image is more likely to be not spam than spam.", correct = TRUE),
  allow_retry = TRUE,
  message = "Pay close attention to the legend and the axes of the plot."
)
```

### Data Integrity

In the process of exploring a dataset, you'll sometimes come across something that will lead you to question how the data were compiled. For example, the variable `num_char` contains the number of characters in the email, in thousands, so it could take decimal values, but it certainly shouldn't take negative values.

You can formulate a test to ensure this variable is behaving as we expect:

```
email %>%
  mutate(num_char_check = num_char < 0) %>%
  select(num_char_check)
```

If the entire dataset was printed out, you'd see a long vector of 3921 logical values indicating for each case in the dataset whether that condition is `TRUE`. Here, the first 10 values all appear to be `FALSE`. To verify that *all* of the cases indeed have non-negative values for `num_char`, we can take the *sum* of this vector:

```
email %>%
  mutate(num_char_check = num_char < 0) %>%
  summarize(sum(num_char_check))
```

This is a handy shortcut. When you do arithmetic on logical values, R treats `TRUE` as `1` and `FALSE` as `0`. Since the sum over the whole vector is zero, you learn that every case in the dataset took a value of `FALSE` in the test. That is, the `num_char` column is behaving as we expect and taking only non-negative values.

Consider the variables `image` and `attach`. You can read about them with `?email`, but the help file is ambiguous: do attached images count as attached files in this dataset?

Design a simple test to determine if images count as attached files. This involves creating a logical condition to compare the values of the two variables, then using `sum()` to assess every case in the dataset. Recall that the logical operators are `<` for *less than*, `<=` for *less than or equal to*, `>` for *greater than*, `>=` for *greater than or equal to*, and `==` for *equal to*.

```
# Test if images count as attachments
email %>%
  mutate(image_gt_attach = ___) %>%
  summarize(___)
```

```
# Test if images count as attachments
email %>%
  mutate(image_gt_attach = (___ > ___)) %>%
  summarize(___)
```

```
# Test if images count as attachments
email %>%
  mutate(image_gt_attach = (image > attach)) %>%
  summarize(___)
```

```
# Test if images count as attachments
email %>%
  mutate(image_gt_attach = (image > attach)) %>%
  summarize(sum(image_gt_attach))
```

```
# Test if images count as attachments
email %>%
  mutate(image_gt_attach = (image > attach)) %>%
  summarize(sum(image_gt_attach))
```

### Answering questions with chains

When you have a specific question about a dataset, you can find your way to an answer by carefully constructing the appropriate chain of R code. For example, consider the following question: 

> "Within non-spam emails, is the typical length of emails shorter for those that were sent to multiple people?"
> This can be answered with the following chain:

```
email %>%
   filter(spam == "not spam") %>%
   group_by(to_multiple) %>%
   summarize(median(num_char))
```

The code makes it clear that you are using `num_char` to measure the length of an email and `median()` as the measure of what is typical. If you run this code, you'll learn that the answer to the question is "yes": the typical length of non-spam sent to multiple people is a bit lower than those sent to only one person.

This chain concluded with summary statistics, but others might end in a plot; it all depends on the question that you're trying to answer.

Build a chain to answer each of the following questions, both about the variable `dollar`. 
- For emails containing the word "dollar", does the typical spam email contain a greater number of occurrences of the word than the typical non-spam email? Create a summary statistic that answers this question.
- If you encounter an email with greater than 10 occurrences of the word "dollar", is it more likely to be spam or not spam? Create a bar plot that answers this question.

```
# Question 1
email %>%
  filter(___) %>%
  group_by(___) %>%
  summarize(___)
# Question 2
email %>%
  filter(___) %>%
  ggplot(aes(x = ___)) +
  geom_bar()
```

```
# Question 1
email %>%
  filter(dollar > 0) %>%
  group_by(___) %>%
  summarize(___)
# Question 2
email %>%
  filter(___) %>%
  ggplot(aes(x = ___)) +
  geom_bar()
```

```
# Question 1
email %>%
  filter(dollar > 0) %>%
  group_by(spam) %>%
  summarize(___)
# Question 2
email %>%
  filter(___) %>%
  ggplot(aes(x = ___)) +
  geom_bar()
```

```
# Question 1
email %>%
  filter(dollar > 0) %>%
  group_by(spam) %>%
  summarize(median(dollar))
# Question 2
email %>%
  filter(___) %>%
  ggplot(aes(x = ___)) +
  geom_bar()
```

```
# Question 1
email %>%
  filter(dollar > 0) %>%
  group_by(spam) %>%
  summarize(median(dollar))
# Question 2
email %>%
  filter(dollar > 10) %>%
  ggplot(aes(x = ___)) +
  geom_bar()
```

```
# Question 1
email %>%
  filter(dollar > 0) %>%
  group_by(spam) %>%
  summarize(median(dollar))
# Question 2
email %>%
  filter(dollar > 10) %>%
  ggplot(aes(x = spam)) +
  geom_bar()
```

## Check-in 2

Let's revisit the exercise where you explored the association between whether an email has an image and whether or not its spam. 

### Spam and images

The plot you created was this bar chart of proportions. We want to emphasize an important, but subtle distinction when discussing proportions like this. This plot shows the proportions of spam or not spam within the subsets of emails that either have an image or do not. Said another way, they are conditioned on the has image variable. We get a slightly different story if we exchange the variables so that we condition on spam. Among emails that are spam, almost none of them have an image, while the proportion within non-spam is larger, but still less than 5%. If you're building a spam filter, a situation where you don't actually get to see the value of spam, it'd make more sense to think about conditioning on the has image variable.

```
email %>%
  mutate(has_image = image == 1) %>%
  ggplot(aes(x = has_image, fill = spam)) +
  geom_bar(position = "fill")
```

In this case, we can tell that this variable would be an awful spam filter by itself.

```
email %>%
  mutate(has_image = image == 1) %>%
  ggplot(aes(x = spam, fill = has_image)) +
  geom_bar(position = "fill")
```

### Ordering bars

When we're working with bar charts, you can often make them more readily interpretable if you give them a sensible ordering. Earlier, we collapsed all emails with at least one exclamation mark into a single level of a new two-level categorical variable.

That led to this bar chart, which was informative, but you might caused you to do a double-take when you first saw it. The plot on the left gets us used to seeing the bar for the zeroes on the left, while in the plot on the right, that bar is on the right side.

Let's go through how we would flip the ordering of those bars so that they agree with the plot on the left.

```
knitr::include_graphics("images/switch-bars.png")
```

The first step is to save the mutated categorical variable back into the dataset. The ordering of the bars isn't determined within the code for the plot, but in the way that R represents that variable. If we call levels on the new variable, it returns NULL. 

```
email <- email %>%
  mutate(zero = exclaim_mess == 0)
levels(email$zero)
```

This is because this variable is actually a logical variable, not a factor. To set the ordering of the levels, let's make it a character variable and then change the order of its levels with the `fct_relevel()` function from the **forcats** package.

```
email <- email %>%
  mutate(
    zero = if_else(exclaim_mess == 0, "TRUE", "FALSE"),
    zero = fct_relevel(zero, "TRUE", "FALSE")
    )
```

Now, when we go to make the plot with the same code, the order of the bars are as we wan them.

```
email %>%
  ggplot(aes(x = zero)) +
  geom_bar() +
  facet_wrap(~spam)
```

Here, we decided to order the bars so that it lined up with the structure of another plot. In other circumstances, you might use other criteria to choose the order including a natural ordering of the levels, arranging the bars in increasing or decreasing order of the height of the bars or alphabetical order, which is the default. In making this decision, you're thinking about emphasizing a particular interpretation of the plot and transitioning from purely exploratory graphics to expository graphics, where you seek to communicate a particular idea. This is a natural development as you continue along the process of your data analysis.

###  

OK, let's return back to the case study.

### What's in a number?

Turn your attention to the variable called `number`. Read more about it by pulling up the help file with `?email`.

To explore the association between this variable and `spam`, select and construct an informative plot. For illustrating relationships between categorical variables, you've seen

- Faceted bar plots
- Side-by-side bar plots
- Stacked and normalized bar plots.

Let's practice constructing a faceted bar plot.

- Construct a faceted bar plot of the association between `number` and `spam`.

```
# Construct plot of
___(email, ___
  ___
  ___
```

```
# Construct plot of number_reordered
ggplot(email, aes(x = number)) +
  geom_bar() +
  facet_wrap(~ spam)
```

### What's in a number interpretation

```
ggplot(email, aes(x = number)) +
  geom_bar() +
  facet_wrap(~spam)
```

```
question("Which of the following interpretations of the plot **is not** valid?",
  answer("Given that an email contains a small number, it is more likely to be 
not spam"),
  answer("Given that an email contains no number, it is more likely to be spam.", correct = TRUE),
  answer("Given that an email contains a big number, it is more likely to be not spam.", correct = TRUE),
  answer("Within both spam and not spam, the most common number is a small one.", message = "Wrong!"),
  incorrect = "Given the kind of number an email contains, we can't say whether it'll be more or less likely to be spam.",
  allow_retry = TRUE
)
```

## Conclusion

In this tutorial on summarizing and visualizing data, our goal was to get you comfortable wading into a new dataset and provide a sense of the considerations and techniques needed to find the most important structure in your data.

### Pie chart vs. bar chart

We started with categorical data, often the domain of the pie chart, and hopefully convinced you that bar charts are a useful tool in finding associations between variables and comparing levels to one another. 

Let's take a look at a comparison of pie vs. bar charts for data on identities of superheroes from comics. We will be visualizing the following distribution. 

```
comics %>%
  count(id)
```

On the bar plot, it's quite difficult to tell how the `NA` group compares to the `Public` group, but this comparison is much easier in the bar plot. Also `Unknown` is barely visible in the bar plot and almost not there in the pie chart.

```
p1 <- comics %>%
  count(id) %>%
  ggplot(aes(x = "", y = n, fill = id))+
  geom_bar(stat="identity", width=1)+
  coord_polar("y", start=0) +
  theme(axis.text = element_blank(),
        axis.ticks = element_blank(),
        panel.grid  = element_blank())
p2 <- ggplot(comics, aes(x = id, fill = id)) +
  geom_bar() +
  guides(fill = FALSE)
p1 / p2
```


### Faceting vs. stacking

We saw how the story can change, depending on if we're visualizing counts or proportions.

```
p1 <- ggplot(comics, aes(y = id))+
  geom_bar() +
  facet_wrap(~align, nrow = 1)
p2 <- ggplot(comics, aes(y = align, fill = id)) +
  geom_bar() 
p1 / p2
```

### Histogram

From there, we moved on to numerical data and a collection of graphical techniques that are important: the histogram, the density plot, and the box plot. Each has its strengths and weaknesses. This density plot smooths out some of the trends but is not overly susceptible to binwidth. The box plot provides information about outlying values. Both the density and the histogram (and not the boxplot) communicate whether the data are unimodal, bimodal, flat, or some other shape. 

Let's take a look at a distribution of the number of appearances of the super heroes.

```
ggplot(comics, aes(x = appearances)) +
    geom_histogram(binwidth = 100)
```

Super skewed!

### Density plot

We can see this in the density plot as well.

```
ggplot(comics, aes(x = appearances)) +
    geom_density()
```

### Side-by-side box plots

Or make side-by-side box plots for comparison across levels of a categorical variable.

```
ggplot(comics, aes(x = appearances, y = id)) +
  geom_boxplot()
```

### Center: mean, median, mode

In the third lesson, we discussed the common numerical measures of a distribution: measures of center, variability, shape, plus the presence of outliers. Our life was made much easier by using the combination of `group_by()` and `summarize()` to compute statistics on different groups within our data.

```
comics %>%
  group_by(id) %>%
  summarise(
    mean = mean(appearances),
    median = median(appearances)
  )
```



You have successfully completed this tutorial.

# [< Back to Section 2](https://bghammill.github.io/ims-02-explore/)



<!-- MathJax -->

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>



