## Exploring categorical data

In this tutorial we'll learn about summarizing and visualizing data.  Sometimes, we refer to the techniques covered in this tutorial as **exploratory data analysis** or EDA.

EDA is all about learning the structure of a dataset through a series of numerical and graphical techniques. When you do EDA, you'll look for both general trends and interesting outliers in your data. You'll also generate questions that will help inform subsequent analysis.

The emphasis of the first two lessons is on visualization: how to create and interpret graphical displays of your data. The third lesson focuses on numerical summaries of distributions and utilizes a simple, but powerful coding paradigm in R. We end with a case study that will allow you to synthesize these concepts and apply them to a more complex dataset that contains information on the characteristics of email that are associated with it being spam.

Before beginning the tutorial, we recommend you have some exposure to the R language and that you've taken the Getting Started with Data tutorial in this series.

Let's get started.

### Introduction 

In this tutorial, you'll be exploring data from a wide range of contexts. The first dataset comes from comic books. Two publishers, Marvel and DC, have created a host of superheroes that have made their way into popular culture. You're probably familiar with Batman and Spiderman, but what about Mor the Mighty?

The comics dataset has information on all comic characters that have been introduced by DC and Marvel. If we type the name of the dataset at the console, we get the first few rows and columns. Here we see that each row, or case, is a different character and each column, or variable, is a different observation made about that character. At the top the output tell us the dimensions of this dataset: over 23,000 cases and 11 variables. Right under the variable names, we see that the first three variables are characters, or the `chr` data type. These are variables that we could potentially consider categorical, as they all have a finite number of levels. The first case in the dataset is Peter Parker, alias: Spiderman, where his alias and character name are included in the first variable `name`. The second column, `id`, shows that Peter Parker's personal identity is kept secret, and the third column tell us that his `align`ment is good; that he's a superhero, not a super villain. If you scroll the table to the right side, you will see several additional variables, including eye color and hair color, almost all of which are also characters.

```{r comics, echo=FALSE}
comics 
```

We can learn the different values of a particular character variable by using the `distinct()` function. It's clear that the alignment variable can be "good" or "neutral", but what other values are possible? Here, we pipe the `comics` data into the `distinct()` function, and then insert the `align` column into the `distinct()` function. Inspecting the resulting table, we learn that there are in fact four possible alignments, "Good", "Bad", "Neutral", and "Reformed criminals". 

```{r levels, echo=TRUE}
comics %>% 
  distinct(align)
```

Good thing we checked that! If we do the same for identity, we learn that there are four possible identities.

```{r levels2, echo=TRUE}
comics %>% 
  distinct(id)
```

A common way to represent the number of cases that fall into each combination of levels of two categorical variables, such as these, is with what's called a "contingency table." Creating a contingency table requires three steps: 

1. use the `count()` function to count the number of observations
2. specify the variables you are interested in **inside** the `count()` function
3. pivot the table from its current "long" format to a "wide" format using the `pivot_wider()` function 

This is what the process looks like:

```{r levels3, echo=TRUE, eval = FALSE}
# to get the long table
comics %>% 
  count(align, id)
  
# to get a wider table
comics %>% 
  count(align, id) %>% 
  pivot_wider(names_from = id, values_from = n)
```

The first two steps should look familiar to you, but the third step is a bit overwhelming. What we are trying to accomplish is to take the long table that is output from the `count()` function and make it into a format that is more familiar.

We want for the values of `align` to be in the rows and the values of `id` to be in the columns. We can use the `pivot_wider()` function to "pivot" our table so that it has this form, by moving the `id` variable to the columns, and filling the values of the table with the count variable (`n`). The `names_from` argument tells R where the names of the new columns are coming from (i.e. what variable), and the `values_from` argument tells R where the values in the table are coming from. Here, the values we want in our table are stored as a variable labeled `n` in our table. 

```{r level3out, echo=FALSE}
comics %>% 
  count(align, id) %>% 
  pivot_wider(names_from = id, values_from = n) %>% 
  kable() %>%
  kable_styling(bootstrap_options = "striped", full_width = F, position = "left")       
```

The output tells us that the most common category, at a count of 4493, was "Bad" characters with "Secret" identities.

While tables of counts can be useful, you can get the bigger picture by translating these counts into a graphic. The graphics that you'll be making in this tutorial utilize the **ggplot2** package, which you got a glimpse of in the previous tutorial. Every ggplot requires that you specify three elements: 

1. the dataset
2. how the variables you are interested in are plotted (aesthetically)
3. layers that describe how the data are represented (e.g. points, lines, histograms)

Generally, your `ggplot()` will look something like this, where the dataset is the first argument in the `ggplot()` function. Next, the variables we are interested in plotting are found inside the the `aes()` function, which stands for aesthetics. Finally, we specify the `geom` function to use when plotting the data. 

```{r formula1, eval=FALSE, message=FALSE, warning=FALSE, include=TRUE}
ggplot(data = [DATASET], aes(x = [X VARIABLE], 
                             y = [Y VARIABLE], 
                             fill = [COLOR VARIABLE])) +
  geom_***()     
```

Here, we're interested in the relationship between two categorical variables, which is represented well by a stacked bar chart. In a bar chart, we plot the counts or frequencies of different levels of a categorical variable, by specifying the categorical variable we want to be on the x-axis, and adding a `geom_bar()` layer to the plot. 

```{r formula2, eval=FALSE, include=TRUE}
ggplot(comics, aes(x = id)) +
  geom_bar() 
```

A stacked bar chart adds another layer to the plot, by dividing each bar into different levels of another variable. This coloring of the bars comes from adding a second categorical variable into the `fill` argument of the `aes()` function. This looks something like this: 

```{r formula3, eval=FALSE, include=TRUE}
ggplot(comics, aes(x = id, fill = align)) +
  geom_bar() 
```

Let's look carefully at how this is constructed: each colored bar segment actually corresponds to a count in our table, with the x-axis and the fill color indicating the category being plotted. Several things pop out, like the fact that there are very few characters whose identities are unknown (since it's nearly flat). The single largest bar segment corresponds to the most common category: characters with secret identities that are also bad. We can look across the identity types and realize that bad is not always the largest category. This indicates that there is indeed an association between alignment and identity.

```{r bar, echo=TRUE}
ggplot(comics, aes(x = id, fill = align)) +
    geom_bar()
```

That should be enough to get started. Now it's your turn to start exploring the data.

### Bar chart expectations

Suppose you've asked 30 people, some young, some old, what their preferred flavor of pie is: apple or pumpkin. Your data could be summarized in a side-by-side barchart. Here are three possibilities for how it might look.

```{r premch1, echo=FALSE, fig.height=3, fig.width=8}
d1 <- data.frame(age = rep(c("young", "old"), c(10, 20)),
                 flavor = c(rep(c("apple", "pumpkin"), c(5, 5)), 
                            rep(c("apple", "pumpkin"), c(10, 10))))
d2 <- data.frame(age = rep(c("young", "old"), c(18, 18)),
                 flavor = c(rep(c("apple", "pumpkin"), c(7, 11)),
                            rep(c("apple", "pumpkin"), c(11, 7))))
d3 <- data.frame(age = rep(c("young", "old"), c(12, 18)),
                 flavor = c(rep(c("apple", "pumpkin"), c(2, 10)), 
                            rep(c("apple", "pumpkin"), c(8, 10))))
p1 <- ggplot(d1, aes(x = age, fill = flavor)) + 
  geom_bar() + 
  ggtitle("Plot 1")
p2 <- ggplot(d2, aes(x = age, fill = flavor)) + 
  geom_bar() + 
  ggtitle("Plot 2")
p3 <- ggplot(d3, aes(x = age, fill = flavor)) + 
  geom_bar() + 
  ggtitle("Plot 3")
grid.arrange(p1, p2, p3, ncol = 3)
```


```{r mch1, echo = FALSE}
question("Which one of the barcharts shows no relationship between `age` and `flavor`? In other words, which plot shows that pie preference is the same for both young and old?",
  answer("Plot 1", correct = TRUE, message = "Nice one!"),
  answer("Plot 2", message = "Hmm, looks like the old prefer apple pies and the young prefer pumpkin pies in Plot 2."),
  answer("Plot 3", message = "Looks like the young really prefer pumpkin pies over apple pies in Plot 3!"),
  allow_retry = TRUE
)
```

<div id="hsb2-mutate-num-char-cat-hint">
**Hint:** Which plot shows a similar *proportion* of people who like apple (or pumpkin) pies in each of the two age groups?
</div>


### Contingency table review

In this lesson you'll continue working with the `comics` dataset introduced previously. This is a collection of characteristics on all of the superheroes created by Marvel and DC comics in the last 80 years.

Let's start by creating a contingency table, which is a useful way to represent the total counts of observations that fall into each combination of the levels of categorical variables. Make sure the contingency table has the different levels of `gender` in the rows, and the different levels of `align` in the columns. 

```{r ex1, exercise=TRUE}
# Print the first rows of the data
# Check levels of align
comics %>% 
  distinct(___)
# Check the levels of gender
# Create a 2-way contingency table
```

```{r ex1-hint-1}
# Print the first rows of the data
glimpse(comics)
# Check levels of align
comics %>% 
  distinct(___)
# Check the levels of gender
# Create a 2-way contingency table
```

```{r ex1-hint-2}
# Print the first rows of the data
glimpse(comics)
# Check levels of align
comics %>% 
  distinct(align)
# Check the levels of gender
comics %>% 
  distinct(gender)
# Create a 2-way contingency table
```

```{r ex1-solution}
# Print the first rows of the data
comics
# Check levels of align
comics %>% 
  distinct(align)
# Check the levels of gender
comics %>% 
  distinct(gender)
# Create a 2-way contingency table
comics %>%
  count(align, gender) %>% 
  pivot_wider(names_from = align, values_from = n)
```

### Dropping levels

The contingency table from the last exercise revealed that there are some levels that have very low counts. To simplify the analysis, it often helps to drop such levels from the dataframe.

In R, this requires two steps: 

1. filter out any rows with the levels that have very low counts
2. remove these levels from the variable with `droplevels()`

We are using the `droplevels()` function to eliminate any levels that have zero counts from a variable. 

The contingency table from the last exercise is available in your workspace as `tab`. We'll use this table and the **dplyr** package to `filter()` levels with few observations from our dataframe, using the following steps:

1. print `tab` to explore which level of `align` has the fewest total entries
2. use `filter()` to filter out all rows of `comics` with that level
3. use `droplevels()` to drop the unused levels from the dataframe 
4. save the simplified dataset as `comics_filtered`


```{r ex2-setup}
tab <- comics %>%
  count(align, gender) %>% 
  pivot_wider(names_from = align, values_from = n)
```

```{r ex2, exercise=TRUE}
# Print tab
___
# Remove align level
comics_filtered <- ___ %>%
  ___(align != ___) %>%
  ___()
# See the result
 comics_filtered
```

```{r ex2-solution}
# Print tab
tab
# Remove align level
comics_filtered <- comics %>%
  filter(align != "Reformed Criminals") %>%
  droplevels()
# See the result
comics_filtered
```

<div id="hsb2-mutate-num-char-cat-hint">
**Hint:** Remember, to filter out observations, the not equal to (`!=`) comparison comes in handy!
</div>


### Side-by-side barcharts

While a contingency table represents the counts numerically, it's often more meaningful to represent them graphically. 

Here you'll construct two side-by-side barcharts of the `comics` data. This exercise will show you that  often there can be two or more options for presenting the same data. Passing the argument `position = "dodge"` to `geom_bar()` tells the function that you want a side-by-side (i.e. not stacked) barchart.

To create these plots, let's carry out the following steps:

1. load the **ggplot2** package.
2. create a side-by-side barchart with `align` on the x-axis, and `fill` the bars with the `gender` of the character  
3. create another side-by-side barchart with `gender` on the x-axis, and `fill` the bars with the `align`ment of the character 

```{r ex3-setup}
comics <- as_tibble(comics)
comics <- comics %>%
  filter(align != "Reformed Criminals") %>%
  droplevels()
```

```{r ex3, exercise=TRUE}
# Load ggplot2
# Create side-by-side barchart of alignment by gender
ggplot(___, aes(x = ___, fill = ___)) + 
  geom_bar(___) 
# Create side-by-side barchart of gender by alignment
ggplot(___, aes(x = ___, fill = ___)) + 
  geom_bar(position = ___)
```

```{r ex3-hint-1}
# Load ggplot2
library(ggplot2)
# Create side-by-side barchart of alignment by gender
ggplot(___, aes(x = ___, fill = ___)) + 
  geom_bar(___) 
# Create side-by-side barchart of gender by alignment
ggplot(___, aes(x = ___, fill = ___)) + 
  geom_bar(position = ___)
```

```{r ex3-hint-2}
# Load ggplot2
library(ggplot2)
# Create side-by-side barchart of alignment by gender
ggplot(comics, aes(x = align, fill = gender)) + 
  geom_bar(position = "dodge") 
# Create side-by-side barchart of gender by alignment
ggplot(___, aes(x = ___, fill = ___)) + 
  geom_bar(position = ___)
```

```{r ex3-solution}
# Load ggplot2
library(ggplot2)
# Create side-by-side barchart of alignment by gender
ggplot(comics, aes(x = align, fill = gender)) + 
  geom_bar(position = "dodge") 
# Create side-by-side barchart of gender by alignment
ggplot(comics, aes(x = gender, fill = align)) + 
  geom_bar(position = "dodge")
```

In many visualizations you make, you may be interested in changing the axis labels on the plot. Here, the axis labels of "gender" and "align" could use some spicing up, to be more descriptive. To change the labels of a `ggplot()`, you add (`+`) a `labs()` layer to the plot, where you can specify the `x`, `y`, and `fill` labels, as well as the plot's `title`. 

```{r}
ggplot(comics, aes(x = gender, fill = align)) + 
  geom_bar(position = "dodge") + 
  labs(x = "Character's Sex", 
       fill = "Alignment of Character", 
       y = "Number of Characters") 
```

### Bar chart interpretation

```{r barchartex, echo=FALSE, fig.height=3, fig.width=8}
comics <- comics %>%
  filter(align != "Reformed Criminals") %>%
  droplevels()
# Create plot of gender by alignment
p1 <- ggplot(comics, aes(x = align, fill = gender)) + 
  geom_bar(position = "dodge")
# Create plot of alignment by gender
p2 <- ggplot(comics, aes(x = gender, fill = align)) + 
  geom_bar(position = "dodge")
grid.arrange(p1, p2, ncol = 2)
```


```{r mc, echo=FALSE}
question("Which of the following interpretations of the bar charts above is **not** valid?",
  answer("Among characters with `Neutral` alignment, males are the most common.", message = "Nope, not quite!"),
  answer("In general, there is an association between gender and alignment."),
  answer("Across all genders, `Bad` is the most common alignment.", correct = TRUE),
  answer("There are more male characters than female characters in this dataset.",  message = "Try again!"), 
  allow_retry = TRUE
)
```

## Counts vs. proportions

You may have noticed in the last exercises that sometimes raw counts of cases can be useful, but often it's the proportions that are more interesting. We can do our best to compute these proportions in our head or we could do it explicitly in R.

### From counts to proportions

Let's return to our table of counts of cases by identity and alignment. If we wanted to instead get a sense of the proportion of all cases that fell into each category, we can modify our previous table. We will need to add a column that calculates the proportion of total observations that were observed in each combination of `id` and `align`. 

After counting the number of observations at each level of `id` and `align`, we use the `mutate()` function to calculate the proportion of observations that fell in those levels. We save these proportions into a new variable named `prop`. Similar to before, we then pivot our table from long to wide, but here we need to specify what columns we are interested in pivoting. This is because we have one additional column in our dataframe which we are not interested in including in our table (`n`). To select variables, we specify a vector of column names to the `id_cols` argument.  

```{r fctp, echo = TRUE, eval = FALSE}
comics %>% 
  count(id, align) %>% 
  mutate(prop = n / sum(n)) %>% 
  pivot_wider(id_cols = c(id, align, prop), 
              names_from = align, values_from = prop)
```

```{r fctp2kable, echo=FALSE}
comics %>% 
  count(id, align) %>% 
  mutate(prop = n / sum(n)) %>% 
  pivot_wider(id_cols = c(id, align, prop), 
              names_from = align, values_from = prop) %>% 
  mutate_if(is.numeric, format, digits = 2) %>% 
  gt() %>%
  tab_style(
    style = cell_fill(color = "lightblue"),
    locations = cells_body(
      columns = vars(Bad),
      rows = Bad >= .20)
  )
```

In the table, we see that the single largest category are characters that are bad and secret at about 29% of characters.

Also note that because these are all proportions out of the whole dataset, the sum of all of these proportions is 1.


### Conditional proportions

If we're curious about systematic associations between variables, we should look to conditional proportions. An example of a conditional proportion is the proportion of public identity characters that are good. To build a table of these conditional proportions, we need to specify a grouping variable **before** we calculate the proportions. We perform this grouping with the `group_by()` function you saw in the last tutorial. 

We see here that around 57% of all secret characters are bad. Because we're conditioning on identity (the value in the rows), every row now sums to 1, since 100% of the identities in each row are split between the three alignments.

**Condition on the rows (i.e. rows sum to 1)**

```{r cp, echo = TRUE, eval = FALSE}
comics %>% 
  count(id, align) %>% 
  group_by(id) %>% 
  mutate(prop = n / sum(n)) %>% 
  pivot_wider(id_cols = c(id, align, prop), 
              names_from = align, values_from = prop)
```

```{r cpkable, echo=FALSE}
comics %>% 
  count(id, align) %>%
  group_by(id) %>% 
  mutate(prop = n / sum(n)) %>% 
  ungroup() %>%
  pivot_wider(id_cols = c(id, align, prop), 
              names_from = align, values_from = prop) %>% 
  mutate_if(is.numeric, format, digits = 2) %>% 
  gt() %>%
  tab_style(
    style = cell_fill(color = "lightblue"),
    locations = cells_body(
      columns = vars(Bad),
      rows = Bad == .57)
  )
## WHY IS THIS TABLE SQASHED?
```

To condition on the columns rather than the rows, we change the variable we are grouping. To condition on the columns, we `group_by()` the `align` variable. After conditioning on the columns, we now see that the columns sum to one. Also, we learn, for example, that the proportion of bad characters that are secret is around 63%.

As the number of cells in these tables gets large, it becomes much easier to make sense of your data using graphics. The bar chart is still a good choice, but we're going to need to add some options.

**Condition on the columns (i.e. columns sum to 1)**

```{r cp2, echo = TRUE, eval = FALSE}
comics %>% 
  count(id, align) %>% 
  group_by(align) %>% 
  mutate(prop = n / sum(n)) %>% 
  pivot_wider(id_cols = c(id, align, prop), 
              names_from = align, values_from = prop)
```

```{r cp2kable, echo = FALSE}
comics %>% 
  count(id, align) %>% 
  group_by(align) %>% 
  mutate(prop = n / sum(n)) %>% 
  ungroup() %>% 
  pivot_wider(id_cols = c(id, align, prop), 
              names_from = align, values_from = prop) %>% 
  mutate_if(is.numeric, format, digits = 2) %>% 
  gt() %>%
  tab_style(
    style = cell_fill(color = "lightblue"),
    locations = cells_body(
      columns = vars(Bad),
      rows = Bad >= .50)
  )
```

</br>

Below is the code to create a bar chart of conditional proportions. In this plot, we want to condition on the variable that is on the x-axis and stretch each bar to add up to a total proportion of 1. To stretch the bars this way, we add the `position = "fill"` option inside the `geom_bar()` function. 

```{r chart, echo=TRUE, fig.height=3, fig.width=5}
ggplot(comics, aes(x = id, fill = align)) +
  geom_bar(position = "fill") 
```

Let's add one additional layer: A change to our y-axis to indicate we're looking at proportions rather than counts! When we run this code, we get a plot that reflects our table of proportions after we had conditioned on `id`, as the bar for every level of `id` adds up to 1.

```{r cbc, echo=TRUE, fig.height=3, fig.width=5}
ggplot(comics, aes(x = id, fill = align)) +
  geom_bar(position = "fill") +
  labs(y = "Proportion of Characters")
```

 While the proportion of secret characters that are bad is still large, it's actually less than the proportion of bad characters in those that are listed as unknown. 

```{r cbc2, echo=FALSE, fig.height=3, fig.width=5}
  ggplot(comics, aes(x = id, fill = align)) +
  geom_bar(position = "fill") +
  ylab("proportion") +
  geom_segment(aes(x=2.55, xend=2.55, y=0.43, yend=1), linetype = 2, size = 1)+
  geom_segment(aes(x=3.55, xend=3.55, y=0.23, yend=1), linetype = 2, size = 1)+
  geom_segment(aes(x=3.45, xend=3.45, y=0.43, yend=1), linetype = 2, size = 1)+
  geom_segment(aes(x=4.45, xend=4.45, y=0.23, yend=1), linetype = 2, size = 1)+
  geom_segment(aes(x=2.55, xend=3.55, y=1, yend=1), linetype = 2, size = 1)+
  geom_segment(aes(x=2.55, xend=3.55, y=0.43, yend=0.43), linetype = 2, size = 1)+
  geom_segment(aes(x=3.55, xend=4.45, y=0.23, yend=0.23), linetype = 2, size = 1)+
  geom_segment(aes(x=3.55, xend=4.45, y=1, yend=1), linetype = 2, size = 1)
```

### Conditional bar chart

We get a very different picture if we condition instead on `align`ment. The only thing we need to change from the previous code is to swap the positions of the names of the variables associated with the `x` and `fill` aesthetics. These changes result in a plot that conditions on alignment. 

In the plot below, we see that within characters that are bad, the greatest proportion of those are indeed secret. This might seem paradoxical, but it's just a result of having different numbers of cases in each single level.

```{r cbc3, eval=FALSE, fig.align='center', fig.height=3, fig.width=5, include=TRUE}
ggplot(comics, aes(x = align, fill = id)) +
  geom_bar(position = "fill") +
  ylab("proportion") 
```


```{r cbc4, echo=FALSE, fig.align='center', fig.height=3, fig.width=5}
ggplot(comics, aes(x = align, fill = id)) +
  geom_bar(position = "fill") +
  ylab("proportion") +
  geom_segment(aes(x=0.55, xend=0.55, y=0, yend=0.63), linetype = 2, size = 1)+
  # leftmost line
  geom_segment(aes(x=1.45, xend=1.45, y=0, yend=0.63), linetype = 2, size = 1)+
  # rightmost line
  geom_segment(aes(x=0.55, xend=1.45, y=0, yend=0), linetype = 2, size = 1)+
  # bottom line
  geom_segment(aes(x=0.55, xend=1.45, y=0.63, yend=0.63), linetype = 2, size = 1)
  # top line
```

### Conditional proportions

The following code generates tables of joint and conditional proportions, respectively. Go ahead and run it in the console:

```{r prob5-setup, echo = FALSE}
comics <- comics %>%
  filter(align != "Reformed Criminals") %>%
  droplevels()
```

```{r prob5, exercise = TRUE}
# Joint proportions
comics %>% 
  count(align, gender) %>% 
  mutate(prop = n / sum(n)) %>% 
  pivot_wider(id_cols = c(align, gender, prop), 
              names_from = gender, values_from = prop)
# Conditional on columns
comics %>% 
  count(align, gender) %>% 
  group_by(gender) %>% 
  mutate(prop = n / sum(n)) %>% 
  pivot_wider(id_cols = c(align, gender, prop), 
              names_from = align, values_from = prop) 
```

```{r mccp5, echo=FALSE}
question("Using the tables above, approximately what proportion of all female characters are good?",
  answer("44%", message = "Nope, try again!"),
  answer("1.3%", message = "Not quite!"),
  answer("13%"),
  answer("51%", correct = TRUE, message = "Nice! To answer this question, you needed to look at how `align` was distributed *within* each `gender`. That is, you wanted to *condition* on the `gender` variable."),
  allow_retry = TRUE
)
```

<div id="hsb2-mutate-num-char-cat-hint">
**Hint:** "Take a look at the table that's conditional on gender!"
</div>

</br>

Bar charts can tell dramatically different stories depending on how we choose to represent the data (e.g. counts or proportions). Furthermore, if proportions are chosen, what the proportions are conditioned on can have a substantial impact on the relationships seen in the plot! To demonstrate this difference, in this exercise you'll construct two bar charts: one of counts and one of proportions.

1. Create a stacked barchart of the *counts* of `gender` within different `align`ments, where 
`align` is plotted on the x-axis.
2.  Create a stacked barchart of the *proportions* of `gender` within different `align`ments, where the bars for each `align`ment are filled, so that they sum to 1. 

```{r ex4, exercise = TRUE}
# Plot counts of gender by align
ggplot(comics, aes(x = ___, fill = ___)) +
  geom_bar()
  
# Plot proportion of gender, conditional on align
ggplot(comics, aes(x = ___, fill = ___)) + 
  geom_bar(position = ___)
```

<div id="hsb2-mutate-num-char-cat-hint">
**Hint:** The code for the second plot is the same as the first, except you'll provide a `position = "fill"` argument to `geom_bar()`.

</div>

```{r ex4-solution}
# Plot of gender by align
ggplot(comics, aes(x = align, fill = gender)) +
  geom_bar()
  
# Plot proportion of gender, conditional on align
ggplot(comics, aes(x = align, fill = gender)) + 
  geom_bar(position = "fill") +
  labs(y = "proportion")
```


## Distribution of one variable

You might not have noticed, but already you've been creating plots that illustrate the relationship between two variables in your dataset. It's a bit unusual to lead with this, but it gets you thinking early about the multivariate structure that is found in most real datasets. Now, let's zoom in on working with just a single variable.

### Marginal distribution

To compute a table of counts for a single variable like `id`, we can use our previous tools for counting a variable with the `count()` function. One way to think of this simpler table is that we've taken the original two-way table (with two categorical variables) and added up the cells across each level of `align`. Since we've added over the margins of the other variables, this is sometimes known as a **marginal distribution**.

```{r md1, eval=FALSE, include=TRUE}
comics %>% 
  count(id)
```

```{r md1kable, echo=FALSE}
comics %>% 
  count(id) %>% 
  kable(format = "html") %>% 
  kable_styling(full_width = F) %>%
  row_spec(1, bold = T, background = "lightblue", color = "white") 
  
```

```{r md2, eval=FALSE, include=TRUE}
comics %>% 
  count(id, align) %>% 
  pivot_wider(names_from = align, values_from = n)
```

```{r md2kable, echo=FALSE}
comics %$% 
  table(id, align) %>% 
  kable(format = "html") %>% 
  kable_styling(full_width = F) %>%
  row_spec(1, bold = T, background = "lightblue", color = "white") 
```

**453 + 640 + 377 = 1470**

### Simple barchart

The syntax to create the simple bar chart is straightforward as well, which we saw at the beginning of the lesson. To do this, we simply remove the `fill = align` argument from the aesthetics (`aes`).

```{r md3, fig.height=3, fig.width=5}
ggplot(comics, aes(x = id)) +
  geom_bar()
```

### Faceting

Another useful way to visualize the distribution of a single variable is to condition on a particular value of another variable. We might be interested, for example, in the distribution of id for all neutral characters. We could either filter the dataset and build a barchart using only cases where `align`ment was "Neutral", or we could use a technique called faceting. Faceting breaks the data into subsets based on the levels of a categorical variable and then constructs a plot for each level.

### Faceted barcharts

To implement faceting in ggplot2, all we need to do is add another layer to our plot. To add a faceting layer we use the `facet_wrap()` function. Inside the `facet_wrap()` function we add a tilde (`~`) and then the name of the variable we want to facet by (e.g. `~align`). This can be read as create the above plot "broken down by align". The result is three simple bar charts side-by-side, the first one corresponding to the distribution of `id` within all cases that have a "Bad" alignment, and so on, for "Good" and "Neutral" alignments.

If this plot feels familiar, it should!

```{r md4, fig.height=3, fig.width=8}
ggplot(comics, aes(x = id)) +
  geom_bar() +
  facet_wrap(~align)
```

### Faceting vs. stacking

In essence, the faceted plot is a rearrangement of the stacked bar charts that we considered at the beginning of the lesson.

```{r fvs, echo=FALSE, fig.height=3, fig.width=8}
plot1 <- ggplot(comics, aes(x = id)) +
  geom_bar() +
  facet_wrap(~align) +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
plot2 <- ggplot(comics, aes(x = align, fill = id)) +
  geom_bar()
gridExtra::grid.arrange(plot1, plot2, nrow = 1)
```

Each facet in the plot on the left corresponds to a single stacked bar in the plot on the right. They allow you to get a sense the distribution of a single variable, by looking at a single facet or a single stacked bar or the association between the variables, by looking across facets or across stacked bars.

```{r lines, echo = FALSE, fig.height=3, fig.width=8}
plot1 <- ggplot(comics, aes(x = id))+
  geom_bar() +
  facet_wrap(~align) + 
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
plot2 <- ggplot(comics, aes(x = align, fill = id)) +
  geom_bar() 
gridExtra::grid.arrange(plot1, plot2, nrow = 1)
grid.rect(x = 0.075, y = 0.27, width = 0.13, height = 0.7, hjust = 0, vjust = 0, gp = gpar(lty = 2, lwd = 2.5, fill = "transparent"))
# around bad facet 
grid.rect(x = 0.22, y = 0.27, width = 0.13, height = 0.7, hjust = 0, vjust = 0, gp = gpar(lty = 3, lwd = 2.5, fill = "transparent"))
#around good facet
grid.rect(x = 0.36, y = 0.27, width = 0.13, height = 0.7, hjust = 0, vjust = 0, gp = gpar(lty = 4, lwd = 2.5, fill = "transparent"))
# around neutral facet
grid.rect(x = 0.587, y = 0.19, width = 0.076, height = 0.75, hjust = 0, vjust = 0, gp = gpar(lty = 2, lwd = 2.5, fill = "transparent"))
# around bad stacked bar
grid.rect(x = 0.675, y = 0.19,width = 0.075, height = 0.65, hjust = 0, vjust = 0, gp = gpar(lty = 3, lwd = 2.5, fill = "transparent"))
# around good stacked bar
grid.rect(x = 0.76, y = 0.19,width = 0.075, height = 0.235, hjust = 0, vjust = 0, gp = gpar(lty = 4, lwd = 2.5, fill = "transparent"))
# around neutral stacked bar
```

A discussion of plots for categorical data wouldn't be complete without some mention of the pie chart.

### Pie chart vs. bar chart

The pie chart is a common way to display categorical data where the size of the slice corresponds to the proportion of cases that are in that level. Here is a pie chart for the identity variable and it looks pleasing enough. The problem with pie charts, though, is that it can be difficult to assess the relative size of the slices. Here, how large would you say the pink "No Dual" slice is? Maybe 5%? What about the green "Public" slice?

If we represent this data using a barchart the answer is obvious: the proportion of public is greater. For that reason, it's generally a good idea to stick to barcharts.

```{r pie, echo=FALSE, fig.height=3, fig.width=6, cache = TRUE}
plot1 <- ggplot(comics, aes(x = "", y = id, fill = id))+
 geom_bar(stat="identity", width=1)+
 coord_polar("y", start=0) +
   theme(axis.text = element_blank(),
        axis.ticks = element_blank(),
        panel.grid  = element_blank())
plot2 <- ggplot(comics, aes(x = id)) +
  geom_bar(aes(y = ..prop.., group = 1))
gridExtra::grid.arrange(plot1, plot2, nrow = 1, widths = c(6, 4))
```

### Marginal barchart

If you are interested in the distribution of alignment of *all* superheroes, it makes sense to construct a barchart for just that single variable.

You can improve the interpretability of the plot, though, by implementing some sensible ordering. Superheroes that are `"Neutral"` show an alignment between `"Good"` and `"Bad"`, so it makes sense to put that bar in the middle. To do this, we first need to convert the `align` variable from a character (`chr`) to a factor (`fct`) data type.

Luckily, R has a built-in `factor()` function specifically for these types of data type conversions. The `factor()` function has two main arguments: 

1. the name of the variable you wish to convert to a factor 
2. the levels of the factor (i.e. the order you want the levels to appear)

**Note:** the `levels` of the variable must be enclosed in quotations, and they must be specified as a vector. We do this by using the concatenate (`c()`) function, which creates a vector from a list of items. 

Converting a variable from a character to a factor looks something like this:

```{r, eval = FALSE}
comics %>% 
  mutate(gender = factor(id, 
                         levels = c("Secret", "Public", "No Dual", "Unknown")
                         )
         )
```

To do this variable conversion we use the following steps:

1. load in the `comics` data
2. use the `mutate()` function to make changes to the `align` variable
3. use the `factor()` function to specify how the `align` variable will be changed
4. save the updated dataset into a new dataframe, named `comics_updated`

Once you've reordered the `align` variable, create a barchart of counts of the
`align` variable.

```{r ex5, exercise = TRUE}
# Change the order of the levels in align
comics_updated <- comics %>% 
  mutate(align = factor(___, 
                       levels = c("___", "___", "___"))
  )
# Create plot of the updated align variable
ggplot(___, aes(x = ___)) + 
  geom_bar()
```

```{r ex5-hint-1}
# Change the order of the levels in align
comics_updated <- comics %>% 
  mutate(align = factor(align, 
                       levels = c("___", "___", "___"))
  )
```

```{r ex5-hint-2}
# Change the order of the levels in align
comics_updated <- comics %>% 
  mutate(align = factor(align, 
                       levels = c("Good", "Neutral", "Bad"))
  )
# Create plot of align
ggplot(___, aes(x = ___)) + 
  geom_bar()
```


```{r ex5-solution}
# Change the order of the levels in align
comics_updated <- comics %>% 
  mutate(align = factor(align, 
                       levels = c("Good", "Neutral", "Bad"))
  )
# Create plot of align
ggplot(comics_updated, aes(x = align)) + 
  geom_bar()
```


### Conditional barchart

Now, if you want to break down the distribution of alignment based on gender, you're looking for conditional distributions.

You could make these by creating multiple filtered datasets (one for each gender) or by faceting the plot of alignment based on gender. As a point of comparison, we've provided your plot of the marginal distribution of alignment from the last exercise.

Create a barchart of `align` faceted by `gender`.

```{r ex6-setup}
comics_updated <- comics %>% 
  mutate(align = factor(align, 
                       levels = c("Good", "Neutral", "Bad"))
  )
ggplot(comics_updated, aes(x = align)) + 
  geom_bar()
```

```{r ex6, exercise = TRUE}
# Plot of alignment broken down by gender
ggplot(comics_updated, aes(x = ___)) + 
  geom_bar() +
  facet_wrap(~ ___)
```

```{r ex6-hint}
Specify align as the x aesthetic, then facet by gender within facet_wrap().
```

```{r ex6-solution}
ggplot(comics_updated, aes(x = align)) + 
  geom_bar() +
  facet_wrap(~ gender)
```


### Improve piechart

The piechart is a very common way to represent the distribution of a single categorical variable, but they can be more difficult to interpret than barcharts.

```{r, echo = FALSE}
flavor <- rep(c("cherry", "key lime", "boston creme", "strawberry", 
                 "blueberry", "apple", "pumpkin"),
               c(13, 16, 15, 11, 14, 17, 12))
pies <- data.frame(flavor = as.factor(flavor))
cnt <- count(pies, flavor)
ggplot(cnt, aes(x = "", y = n, fill = flavor)) +
  geom_bar(width = 1, stat = "identity") +
  coord_polar("y", start = pi / 3) +
  xlab("") + 
  ylab("") + 
  theme(panel.grid.minor = element_blank(),
        panel.grid.major = element_blank(),
        axis.ticks = element_blank(),
        axis.text = element_blank())
```

This is a piechart of a dataset called `pies` that contains the favorite pie flavors of 98 people. Improve the representation of these data by constructing a *barchart* that is ordered in descending order of count.

- Use the code provided to reorder the levels of `flavor` so that they're in descending order by count.
- Create a barchart of `flavor`. The default coloring may look drab by comparison, so change the `fill` of the bars to `"chartreuse"`. 
- Plot `flavor` on the y-axis, so that the labels of the flavors don't smoosh together! 

```{r ex7, exercise = TRUE}
# Discover what the counts of pie flavors are 
pies %>% 
  count(flavor)
# Put levels of flavor in descending order
pies_updated <- pies %>% 
  mutate(flavor = factor(flavor, 
                         levels = c("apple", "key lime", "boston creme",
                                    "blueberry", "cherry", "pumpkin", "strawberry")
  )
  )
# Create barchart of flavor
# Using plot flavor on y-axis so label text fits
ggplot(___, aes(y = ___)) + 
  geom_bar(fill = ___) 
```

```{r ex7-solution}
# Create barchart of flavor
# Using plot flavor on y-axis so label text fits
ggplot(pies_updated, aes(y = flavor)) + 
  geom_bar(fill = "chartreuse") 
```


## Congratulations!

You have successfully completed Lesson 1 in Tutorial 2: Summarizing and visualizing data.

What's next?

`r emo::ji("ledger")` [Full list of tutorials supporting OpenIntro::Introduction to Modern Statistics](https://openintrostat.github.io/ims-tutorials/)

`r emo::ji("spiral_notepad")` [Tutorial 2: Getting Started with Data](https://openintrostat.github.io/ims-tutorials/02-summarizing-and-visualizing-data/)

`r emo::ji("one")` [Tutorial 2 - Lesson 1: Visualizing categorical data](https://openintro.shinyapps.io/ims-02-summarizing-and-visualizing-data-01/)

`r emo::ji("one")` [Tutorial 2 - Lesson 2: Visualizing numerical data](https://openintro.shinyapps.io/ims-02-summarizing-and-visualizing-data-02/)

`r emo::ji("one")` [Tutorial 2 - Lesson 3: Summarizing with statistics](https://openintro.shinyapps.io/ims-02-summarizing-and-visualizing-data-03/)

`r emo::ji("one")` [Tutorial 2 - Lesson 4: Case study](https://openintro.shinyapps.io/ims-02-summarizing-and-visualizing-data-04/)

`r emo::ji("open_book")` [Learn more at Introduction to Modern Statistics](http://openintro-ims.netlify.app/)