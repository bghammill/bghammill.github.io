## Exploring categorical data

In this tutorial we'll learn about summarizing and visualizing data.  Sometimes, we refer to the techniques covered in this tutorial as **exploratory data analysis** or EDA.

EDA is all about learning the structure of a dataset through a series of numerical and graphical techniques. When you do EDA, you'll look for both general trends and interesting outliers in your data. You'll also generate questions that will help inform subsequent analysis.

The emphasis of the first two lessons is on visualization: how to create and interpret graphical displays of your data. The third lesson focuses on numerical summaries of distributions. We end with a case study that will allow you to synthesize these concepts and apply them to a more complex dataset that contains information on the characteristics of email that are associated with it being spam.

Before beginning the tutorial, we recommend you have some exposure to the SAS language and that you've taken the Getting Started with Data tutorial in this series.

Let's get started.

### Introduction 

In this tutorial, you'll be exploring data from a wide range of contexts. The first dataset comes from comic books. Two publishers, Marvel and DC, have created a host of superheroes that have made their way into popular culture. You're probably familiar with Batman and Spiderman, but what about Mor the Mighty?

The comics dataset has information on all comic characters that have been introduced by DC and Marvel. Let's make a working copy of that dataset and check it out:

```
* Initialize things if you have not done this already during this SAS session;
%include "~/my_shared_file_links/hammi002/sasprog/run_first.sas";

* Makes and checks a working copy of COMICS data;
%use_data(comics);
%glimpse(comics);
```

We see that each row, or observation, is a different character and each column, or variable, is a different piece of information about that character. The top the output tells us the dimensions of this dataset: over 23,000 observations and 11 variables. In the variable list, we see that many are character types, which may indicate that they are categorical variables.

The first row in the dataset is Peter Parker, alias: Spiderman, where his alias and character name are included in the first variable `name`. The second column, `id`, shows that Peter Parker's personal identity is kept secret, and the third column tell us that his `align`ment is good; that he's a superhero, not a super villain. If you scroll the table to the right side, you will see several additional variables, including eye color and hair color.

We can learn the different values of a particular character variable by using `PROC FREQ` to generate tables for each variable. It's clear that the alignment variable can be "good" or "neutral", but what other values are possible?  

```
* Check distribution of ALIGN variable;
proc freq data=comics;
	tables align / missing;
run;
```

Inspecting the resulting table, we learn that there are in fact four possible alignments, "Good", "Bad", "Neutral", and "Reformed criminals". Good thing we checked that! .

If we do the same for identity, we learn that there are four possible identities.

```
* Check distribution of ID variable;
proc freq data=comics;
	tables id / missing;
run;
```

Also note, based on the output so far, that this is the first dataset we've worked with that has some missing data. For this tutorial, we will generally ignore missing data and just report percentages and generate figures for non-missing data. In practice, you'll want to consult a statistician about how best to handle missing data in analyses

A common way to represent the number of cases that fall into each combination of levels of two categorical variables, such as these, is with what's called a "contingency table." Creating a contingency table is a simple extension of the `PROC FREQ` code above: 

```
* Check counts of ALIGN by ID variables;
proc freq data=comics;
	tables align * id / norow nopct nocol;
run;
```

Some things to notice about this code:

* We list `align` first because we want that variable to be our row variable
* We list `id` second, because we want that variable to be our column variable
* We added the `norow`, `nopct`, and `nocol` options here since we only wanted to look at counts for now (no percentages) 
* We have removed the `missing` option, so we get the counts only for characters where information on both variables is known. In this case, SAS does tell us how many observations were excluded due to missing data, at the bottom of the table, but does not included those records in the cell counts above.

The output tells us that the most common category, at a count of 4492, was characters with "Bad" alignment with "Secret" identities.

While tables of counts can be useful, you can get the bigger picture by translating these counts into a graphic. The graphics that you'll be making in this tutorial utilize `PROC SGPLOT`, which you saw in the previous tutorial. 

Here, we're interested in the relationship between two categorical variables, which is represented well by a stacked bar chart. In a bar chart, we plot the counts or frequencies of different levels of a categorical variable, by specifying the categorical variable we want to be on the x-axis: 

```
* Simple bar chart of ID;
proc sgplot data=comics;
	vbar id;
run;
```

A stacked bar chart adds another layer to the plot, by dividing each bar into different levels of another variable. This coloring of the bars comes from adding a group variable to the `vbar` statement above: 

```
* Stacked bar chart of ID, with categories of alignment;
proc sgplot data=comics;
	vbar id / group=align;
run;
```

Let's look carefully at how this is constructed: each colored bar segment actually corresponds to a count in our table, with the x-axis and the fill color indicating the category being plotted. Several things pop out, like the fact that there are very few characters whose identities are unknown (since it's nearly flat). The single largest bar segment corresponds to the most common category: characters with secret identities that are also bad. We can look across the identity types and realize that bad is not always the largest category. This indicates that there is indeed an association between alignment and identity.

That should be enough to get started. Now it's your turn to start exploring the data.

### Bar chart expectations

Suppose you've asked 30 people, some young, some old, what their preferred flavor of pie is: apple or pumpkin. Your data could be summarized in a side-by-side bar chart. Here are three possibilities for how it might look.

![](images/premch1-1.png)

*Which one of the barcharts shows no relationship between `age` and `flavor`? In other words, which plot shows that pie preference is the same for both young and old?*

**Hint:** Which plot shows a similar *proportion* of people who like apple (or pumpkin) pies in each of the two age groups?

### Contingency table review

Let's continue working with the `comics` dataset to create a different contingency table, which is a useful way to represent the total counts of observations that fall into each combination of the levels of categorical variables. Make sure the contingency table has the different levels of `gender` in the rows, and the different levels of `align` in the columns. 

```
* Check counts of GENDER by ALIGN variables;
proc freq data=comics;
	tables gender * align / norow nopct nocol;
run;
```

### Dropping levels

This contingency table revealed that there are some levels that have very low counts. To simplify the analysis, it sometimes helps to ignore such levels from the dataset using a `where` statement. For example, let's create a new dataset that excludes the reformed criminals, then rerun this contingency table:

```
* Create new dataset without reformed criminals;
data comics_no_rc;
	set comics;
	where align ne "Reformed Criminals";
run;

* Check counts of GENDER by ALIGN variables, ignoring reformed criminals;
proc freq data=comics_no_rc;
	tables gender * align / norow nopct nocol;
run;
```

We'll continue to use this filtered dataset below.

### Side-by-side barcharts

While a contingency table represents the counts numerically, it's often more meaningful to represent them graphically. 

The code below creates two bar charts:

1. One with `align` on the x-axis, and bars sized & colored by the `gender` of the character
2. One with `gender` on the x-axis, and bars sized & colored by the `align` of the character

```
* Side-by-side bar chart of alignment, with categories of gender;
proc sgplot data=comics_no_rc;
	vbar align / group=gender groupdisplay=cluster;
run;

* Side-by-side bar chart of gender, with categories of alignment;
proc sgplot data=comics_no_rc;
	vbar gender / group=align groupdisplay=cluster;
run;
```

### Bar chart interpretation

![](images/barchartex-1.png)

*Which of the following interpretations of the bar charts above is **not** valid?*

* Among characters with `Neutral` alignment, males are the most common
* In general, there is an association between gender and alignment
* Across all genders, `Bad` is the most common alignment
* There are more male characters than female characters in this dataset

## Counts vs. proportions

You may have noticed in the last exercises that sometimes raw counts of cases can be useful, but often it's the proportions that are more interesting. We can do our best to compute these proportions in our head or we could do it explicitly in SAS.

### From counts to proportions

Let's return to our table of counts of cases by identity and alignment. If we wanted to instead get a sense of the proportion of all cases that fell into each category, we can modify our previous table to add this information. `PROC FREQ` does this easily for us. Here, again, is the distribution of records for each combination of `id` and `align`. 

```
* Check distribution of ALIGN by ID variables, include overall proportions;
proc freq data=comics_no_rc;
	tables align * id;
run;
```

Below each frequency count, we now see three proportions. And while this is a little busy, everything we might want to know about the distribution of these two characteristics is here.

Note the legend that appears to the top left of the table:

![](images/freq-props.png)

This tell us what each number in each table cell represents. Right below the frequency is the overall percent, or what proportion of all records fall into that particular cell. We see, for example, that the single largest category are characters that are bad + secret at about 29% of characters.

But what about the row and column percents?


### Conditional proportions

If we're curious about systematic associations between variables, we should look to conditional proportions. Remember that conditional proportions change the denominator of the calculation to be the size of the specific group of interest, instead of the size of the total population.

An examples of a conditional proportion is the proportion of public identity characters that are bad. Given the set-up of our contingency table (alignment as rows and id as columns), we can answer this question using the column percents (last number in each cell) within the public identity column. If you look up the public + bad cell, you can see that about 36% of public identity characters are bad.

Another example of a conditional proportion is the proportion of bad characters that are secret. Given the set-up of our contingency table, we can answer this question using the row percents (third number in each cell) within the bad alignment row. If you look up the bad + secret, you can see that nearly 63% of bad characters are secret.

Remember, when you condition on alignment (the value in the rows), the row percents sum to 100, since all of the identities in each row are split between the three ids.

Because `PROC FREQ` output can be busy, it is often helpful to use the `norow`, `nopct`, and `nocol` options to limit the output. For example:

```
* Check distribution of ALIGN by ID variables, row percents only;
proc freq data=comics_no_rc;
	tables align * id / nocol nopct;
run;
```

As the number of cells in these tables gets large, it becomes much easier to make sense of your data using graphics. The bar chart is still a good choice, but we're going to need to add some options.

**Condition on the columns (i.e. columns sum to 1)**

```
comics %>% 
  count(id, align) %>% 
  group_by(align) %>% 
  mutate(prop = n / sum(n)) %>% 
  pivot_wider(id_cols = c(id, align, prop), 
              names_from = align, values_from = prop)
```

```
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

```
ggplot(comics, aes(x = id, fill = align)) +
  geom_bar(position = "fill") 
```

Let's add one additional layer: A change to our y-axis to indicate we're looking at proportions rather than counts! When we run this code, we get a plot that reflects our table of proportions after we had conditioned on `id`, as the bar for every level of `id` adds up to 1.

```
ggplot(comics, aes(x = id, fill = align)) +
  geom_bar(position = "fill") +
  labs(y = "Proportion of Characters")
```

 While the proportion of secret characters that are bad is still large, it's actually less than the proportion of bad characters in those that are listed as unknown. 

```
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

```
ggplot(comics, aes(x = align, fill = id)) +
  geom_bar(position = "fill") +
  ylab("proportion") 
```


```
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

```
comics <- comics %>%
  filter(align != "Reformed Criminals") %>%
  droplevels()
```

```
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

```
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

```
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

```
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

```
comics %>% 
  count(id)
```

```
comics %>% 
  count(id) %>% 
  kable(format = "html") %>% 
  kable_styling(full_width = F) %>%
  row_spec(1, bold = T, background = "lightblue", color = "white") 
  
```

```
comics %>% 
  count(id, align) %>% 
  pivot_wider(names_from = align, values_from = n)
```

```
comics %$% 
  table(id, align) %>% 
  kable(format = "html") %>% 
  kable_styling(full_width = F) %>%
  row_spec(1, bold = T, background = "lightblue", color = "white") 
```

**453 + 640 + 377 = 1470**

### Simple barchart

The syntax to create the simple bar chart is straightforward as well, which we saw at the beginning of the lesson. To do this, we simply remove the `fill = align` argument from the aesthetics (`aes`).

```
ggplot(comics, aes(x = id)) +
  geom_bar()
```

### Faceting

Another useful way to visualize the distribution of a single variable is to condition on a particular value of another variable. We might be interested, for example, in the distribution of id for all neutral characters. We could either filter the dataset and build a barchart using only cases where `align`ment was "Neutral", or we could use a technique called faceting. Faceting breaks the data into subsets based on the levels of a categorical variable and then constructs a plot for each level.

### Faceted barcharts

To implement faceting in ggplot2, all we need to do is add another layer to our plot. To add a faceting layer we use the `facet_wrap()` function. Inside the `facet_wrap()` function we add a tilde (`~`) and then the name of the variable we want to facet by (e.g. `~align`). This can be read as create the above plot "broken down by align". The result is three simple bar charts side-by-side, the first one corresponding to the distribution of `id` within all cases that have a "Bad" alignment, and so on, for "Good" and "Neutral" alignments.

If this plot feels familiar, it should!

```
ggplot(comics, aes(x = id)) +
  geom_bar() +
  facet_wrap(~align)
```

### Faceting vs. stacking

In essence, the faceted plot is a rearrangement of the stacked bar charts that we considered at the beginning of the lesson.

```
plot1 <- ggplot(comics, aes(x = id)) +
  geom_bar() +
  facet_wrap(~align) +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
plot2 <- ggplot(comics, aes(x = align, fill = id)) +
  geom_bar()
gridExtra::grid.arrange(plot1, plot2, nrow = 1)
```

Each facet in the plot on the left corresponds to a single stacked bar in the plot on the right. They allow you to get a sense the distribution of a single variable, by looking at a single facet or a single stacked bar or the association between the variables, by looking across facets or across stacked bars.

```
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

```
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

```
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

```
# Change the order of the levels in align
comics_updated <- comics %>% 
  mutate(align = factor(___, 
                       levels = c("___", "___", "___"))
  )
# Create plot of the updated align variable
ggplot(___, aes(x = ___)) + 
  geom_bar()
```

```
# Change the order of the levels in align
comics_updated <- comics %>% 
  mutate(align = factor(align, 
                       levels = c("___", "___", "___"))
  )
```

```
# Change the order of the levels in align
comics_updated <- comics %>% 
  mutate(align = factor(align, 
                       levels = c("Good", "Neutral", "Bad"))
  )
# Create plot of align
ggplot(___, aes(x = ___)) + 
  geom_bar()
```


```
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

```
comics_updated <- comics %>% 
  mutate(align = factor(align, 
                       levels = c("Good", "Neutral", "Bad"))
  )
ggplot(comics_updated, aes(x = align)) + 
  geom_bar()
```

```
# Plot of alignment broken down by gender
ggplot(comics_updated, aes(x = ___)) + 
  geom_bar() +
  facet_wrap(~ ___)
```

```
Specify align as the x aesthetic, then facet by gender within facet_wrap().
```

```
ggplot(comics_updated, aes(x = align)) + 
  geom_bar() +
  facet_wrap(~ gender)
```


### Improve piechart

The piechart is a very common way to represent the distribution of a single categorical variable, but they can be more difficult to interpret than barcharts.

```
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

```
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

```
# Create barchart of flavor
# Using plot flavor on y-axis so label text fits
ggplot(pies_updated, aes(y = flavor)) + 
  geom_bar(fill = "chartreuse") 
```


## Congratulations!

You have successfully completed Lesson 1 in Tutorial 2: Summarizing and visualizing data.

What's next?

[Full list of tutorials supporting OpenIntro::Introduction to Modern Statistics](https://bghammill.github.io/)

[Tutorial 2: Getting Started with Data](https://bghammill.github.io/ims-02-explore/)

- [Tutorial 2 - Lesson 1: Visualizing categorical data](https://bghammill.github.io/ims-02-explore/ims-02-lesson-01/)
- [Tutorial 2 - Lesson 2: Visualizing numerical data](https://bghammill.github.io/ims-02-explore/ims-02-lesson-02/)
- [Tutorial 2 - Lesson 3: Summarizing with statistics](https://bghammill.github.io/ims-02-explore/ims-02-lesson-03/)
- [Tutorial 2 - Lesson 4: Case study](https://bghammill.github.io/ims-02-explore/ims-02-lesson-04/)

[Learn more at Introduction to Modern Statistics](http://openintro-ims.netlify.app/)

<!-- MathJax -->

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>



