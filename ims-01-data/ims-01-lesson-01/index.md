## Language of Data

Hello, and welcome to **Getting Started with Data**!

In this tutorial we will take you through concepts and SAS code that are essential for getting started with data analysis.

Scientists seek to answer questions using rigorous methods and careful observations. These observations form the backbone of a statistical investigation and are called data. Statistics is the study of how best to collect, analyze, and draw conclusions from data. It is helpful to put statistics in the context of a general process of investigation:

- **Step 1**: Identify a question or problem.

- **Step 2**: Collect relevant data on the topic.

- **Step 3**: Analyze the data.

- **Step 4**: Form a conclusion.

We will focus on **steps 1 and 2** of this process in this tutorial.

Our learning goals for the tutorial are *to internalize the language of data, load and view a dataset in SAS and distinguish between various variable types and data representations, classify a study as observational or experimental, and determine the scope of inference, distinguish between various sampling strategies, and identify the principles of experimental design.*

This tutorial does not assume any previous SAS experience, but you should be familiar with SAS from the SAS Bootcamp and will learn more about SAS over the course of the semester in PHS 703.

## Macros

Macros in SAS are packages of pre-written code that allow users to perform specific tasks without having to write the code for those tasks themselves. We will rely on a few macros in these tutorials to do some simple things like making working copies of course data sets and checking the contents of datasets.

Macros in SAS are identified by the `%` sign. For example, if you see `%use_data(census2010)` in a SAS program, this line is calling the `%use_data` macro with the `census2010` argument. The name, arguments, and details of the macro can by found by looking at the macro code itself.

For example, here are 2 macros that we will use frequently in these tutorials:

* **%use_data(ds)**: This macro makes a working copy of a stored dataset for you to use and modify. Indicate the name of that dataset as the argument to the macro.
* **%glimpse(ds)**: This macro outputs the contents of a dataset 

It is important to note that these macros are not generally available to use unless and until you define them. We will do that by running the following short SAS program every time we start a new tutorial. This program tells SAS where the course dataset library is located, how to find the relevant data formats, and defines the two macros described above.

```
%include "~/my_shared_file_links/hammi002/sasprog/run_first.sas";
```

Run this code now by opening a new program in SAS Studio (using the F4 key), copying the code from here to that new program, and clicking ![Running man](images/running-man.png)(*Run*). You'll do with with any code in a code block like above, by the way. As with any program in SAS, after running, check to log to ensure SAS did not encounter any errors.

If you are curious, feel free to open this file to see exactly the SAS code within the `%use_data` and `%glimpse` macros.

## Data in SAS 

In this lesson, we'll begin by introducing the terminology of datasets and data in SAS.

One of the datasets that we will work with in this tutorial comes from the High School and Beyond Survey, which is a survey conducted on high school seniors by the National Center of Education Statistics.

The data are organized in what we call a *data set*, where each row represents an *observation* (or *record* or *case*) and each column represents a *variable* (or *field*). If you ever use spreadsheets, such as Excel, this representation should be familiar to you.

In this lesson we'll work with the High School and Beyond dataset, stored in the course SAS dataset library. The data are stored in a **data set** called `hsb2`. 

To make a copy of this dataset to use, copy/paste and run the following code:

```
* Make a working copy of the HSB2 dataset for your use;
%use_data(hsb2);
```

Because we aren't requesting any output, SAS Studio should take you to the *Output Data*  tab, so you can preview the dataset. You can use the scroll bars on the right and bottom of the window to scroll through the records and variables in the dataset, respectively.

### Loading data into SAS

Note that there are many ways of loading data into SAS, depending on where and how your data are stored. In this lesson, we're using a dataset that is located in the PHS 701 dataset library. Other commonly used formats of data are plain text files (.txt), comma-separated value files (.csv), and Excel files (.xls or .xlsx). 

In all the tutorials in these series, we will use existing data from the PHS 701 library, so you won't need to worry about the myriad ways of loading data into SAS. However, learning more about loading data into SAS is important for when you're working on an analysis with your own data. You should learn about this in PHS 703, but other resources are available (for example, from [SAS documentation](https://documentation.sas.com/doc/en/webeditorcdc/5.2/webeditorug/p11uw39h8jb27on1fc3d0og7ac52.htm) or from [a different SAS tutorial](https://www.csub.edu/~bzeng/3210/documents/notes/2_Getting%20Your%20Data%20into%20SAS%20The%20Basics.pdf)).  

### Take a peek

When you want to work with data in SAS, a good first step is to take a peek at what
the data look like. The `%glimpse()` macro we have defined is one good way of doing this. Copy/paste and run the code below: 

```
* Gives you a glimpse into the data;
%glimpse(hsb2);
```

The output of `%glimpse` includes output from `PROC CONTENTS` . The top section of this output includes information about the dataset itself. Make sure you can find the following key information here:

* How many records are in the dataset? (200, see *Observations*)
* How may variables are in the dataset? (11, see *Variables*)
* What is the short description of the dataset? (see *Label*, but know that this won't always be present)

The next section, titled *Engine/Host Dependent Information*, includes more technical information about the dataset and can generally be ignored.

The final section of the `PROC CONTENTS` output, titled *List of Variables and Attributes*, is essential, however. For each variable, it lists that variable's name, data type (<u>Num</u>eric or <u>Char</u>acter), label (if assigned), and format (if used). Before we look into this section in more detail, also note that `%glimpse` prints out the variable values of the first 10 records.

Based on both this last section of `PROC CONTENTS` output and the first 10 records of the dataset, make sure you understand the following information:

* What type of data (numeric or character) is held in the `gender` variable? (Character)
* Based on the variable label, what are the values of the `gender` variable? ("male", "female"). Confirm that you see these values in the sample records.
* What type of data (numeric or character) is held in the `math` variable? (Numeric) Confirm that you see these types of values in the sample records.
* What type of data (numeric or character) is held in the `ses` variable? (Numeric)
* Is there a format listed for the `ses` variable? (Yes, SES.) This is very important to note.
* Based on the variable label, what are the valid values of the `ses` variable? ("low", "middle", "high"). What values do you instead see in the sample records?

Well, that `ses` variable is confusing, isn't it? The data type is numeric, but the expected values are character. This is where the format comes in. SAS formats are tricky, so I want to make sure you understand how we will use them in the course datasets. The most important thing to know about SAS formats is that they assign a label to a specific data value. 

For `ses`, this means assigning the label "Low" to the numeric value 1, "Middle" to the numeric value 2, and "High" to the numeric value 3. In this course, we actually include the underlying data value in the label, to help you understand this relationship. That's why, in the sample records, you see "1: low", "2: middle", and "3: high". The numeric values (1, 2, and 3) are what is stored in the SAS dataset to represent each of the different labels.

A key point to remember (and we'll reiterate this later) is that when you interact with the `ses` variable, *you will have to use the numeric, unformatted values* in order for SAS to know what we're talking about. 

**Practice time**

Next, we'll practice on another dataset, `email50`, which contains a subset of incoming emails for the first three months of 2012 for a single email account. Make a working copy of the dataset and examine its structure.

```
%use_data(email50);
%glimpse(email50);
```

Can you answer the following questions?

* How many observations are there?
* How many variables are there?
* Are there any character variables in this dataset?
* What kind of information is stored in the `exclaim_mess` variable?
* Are formats assigned to any of the variables in this dataset? If so, which ones?
* Looking at the first observation, what does the "0: No" value of the `spam` variable indicate? What is the actual value of the data stored in that field?

## Types of variables

When you first start working with a dataset, it's good practice to take a note of its dimensions; how many rows or observations and how many columns or variables the data frame has.

You learned how to do this in the previous section using the `%glimpse` macro. You also learned how to take a quick look at the list of variables in the dataset. In this section, we will delve deeper into the classification of variables as **numerical or categorical**. This is an important step, as the type of variable helps us determine what summary statistics to calculate, what type of visualizations to make, and what statistical method will be appropriate to answer the research questions we're exploring.

There are two types of variables: numerical and categorical.

- **Numerical**, in other words, quantitative, variables take on numerical values. It is sensible to add, subtract, take averages, and so on, with these values.

- **Categorical**, or qualitative, variables, take on a limited number of distinct categories. These categories can be identified with character labels or with numbers. For example, it is customary to see likert scale variables (strongly agree to strongly disagree) coded as 1 through 5, but it wouldn't be sensible to do arithmetic operations with these values. Similarly, in the prior section, we saw that we can assign formats to numeric data to represent different non-numeric labels. In both these cases, the numeric data stored in the SAS variable are merely placeholders for the levels of the categorical variable.

### Numerical data

Numeric variables can be further categorized as **continuous or discrete**.

- **Continuous numeric** variables are usually measured, such as height. These variables can take on an infinite number of values within a given range.

- **Discrete numerical** variables are those that take on one of a specific set of numeric values where we are able to count or enumerate all of the possibilities. One example of a discrete variable is number of pets in a household. In general, count data are an example of discrete variables.

When determining whether a numerical variable is continuous or discrete, it is important to think about the nature of the variable and not just the observed value, as rounding of continuous variables can make them appear to be discrete. For example, height is a continuous variable, however we tend to report our height rounded to the nearest unit of measure, like inches or centimeters.

### Categorical data

Categorical variables that have ordered levels are called **ordinal**.

Think about a survey question where you're asked how satisfied you are with the customer service you received and the options are very unsatisfied, unsatisfied, neutral, satisfied, and very satisfied. These levels have an inherent ordering, hence the variable would be called ordinal.

If the levels of a categorical variable do not have an inherent ordering to them, then the variable is simply called categorical. For example, do you consume caffeine or not?

### Variables in `hsb2`

Let's take a moment to go through the variables in the High School and Beyond dataset again:

```
%glimpse(hsb2);
```

Using the `%glimpse` macro, we can obtain a list of the variables in the dataset and also see what the values stored in these variables look like.

The first variable is `id`, which is an identifier variable for the student. Strictly speaking, this is a categorical variable, though the labeling of this variable is likely not that useful since we would not use this variable in an analysis of relationships between the variables in the dataset. You can think of this variable as being an anonymized version to having the names of the students in the dataset.

The next variable is `gender`, a categorical variable, with levels `"male"` and
`"female"`. It should be noted that the language of government surveys, such as
High School and Beyond, is slow to change. So with these types of data, you will
continue to see variables mislabeled as "gender" when they in fact measure the
biological sex (male, female) of the participant.  

There is no inherent ordering to the levels of this variable, no matter what
anyone tells you! So, this is just a categorical variable. The same is true for
the `race` variable, which has levels of `"white"`, `"african american"`,
`"hispanic"`, and `"asian"`.

Socio-economic status (`ses`) on the other hand, has three levels `"low"`, `"middle"`,
and `"high"` that have an inherent ordering, hence this variable is an *ordinal*
categorical variable.

School type (`schtyp`) and program (`prog`) are also both categorical variables, with no inherent ordering to their levels.

The remaining variables are scores that these students received in reading (`read`), writing (`write`), math (`math`), science (`science`), and social studies  (`socst`) tests. Since these scores are all whole numbers, and assuming that it is not possible to obtain a non-whole number score on these tests, these variables are discrete numerical.

Next we will practice identifying variables in a different dataset.

###  Variables in `email50`

Let's have another look at the `email50` data, so we can practice identifying variable types.

Use the `%glimpse` macro to view the variables in the `email50` dataset.

```
%glimpse(email50);
```

*Review the output to identify each variable as either numerical or categorical, and further as discrete or continuous (if numerical) or ordinal or not ordinal (if categorical).*

## Categorical data in SAS

A common step in many analyses that involve categorical data is a subgroup analysis, where we work with only a subset of the data. For example, analyzing data only from students in public schools or only for students who identified as female. We can obtain these subsets by either using `where` statements in SAS procedures or by creating a new copy of the dataset, filtered for the specific level(s) we're interested in. The latter approach is fine for small datasets (and what we'll do below), but can be wasteful of resources when working with very large datasets.

Suppose we want to do an analysis of only the students in public schools in the High School and Beyond dataset. Let's first find out how many such students there are. One option for obtaining this information in SAS is `PROC FREQ`. This basic SAS procedure is very useful for presenting and analyzing categorical data. In this case we're interested in the number of students for each level of the `schtyp` (school type) column:

```
* Frequency table for school type;
proc freq data=hsb2;
	tables schtyp / missing;
run;
```

You should see that there are 168 students in public schools and 32 in private schools.

The `missing` option on the `tables` statement is very helpful for checking to see if your variable has any missing data. In these data, there is no missing data for school type.

Next, let's create a new dataset that includes only public school students: 

```
* New dataset including only public school students;
data hsb2_public;
	set hsb2;
	where schtyp = 1;
run;
```

We can read the above code as: "create a new dataset, `hsb2_public`, by using the `hsb2` dataset *where* school type is equal to 1. This is an instance where it is essential to know that the school type variable (`schtyp`) is formatted, and that the underlying numeric value of 1 is associated with the desired school type of "public". 

Let's check the frequency distribution of `schtyp` to ensure that this subset worked:

```
* Frequency table for school type;
proc freq data=hsb2;
	tables schtyp / missing;
run;
```

As an aside, because the school type variable contains numeric values to represent different text labels (i.e., is formatted), the following code would not work to achieve this same subsetting:

```
* Cannot use formatted values to subset a dataset;
data hsb2_public;
	set hsb2;
	where schtyp = "public";
run;
```

You can try it if you'd like, but you'll get an error that the where clause has incompatible variables. This just means that we were trying to subset a variable that contains numeric data using a text value. 

Note that if we wanted to create a subset of the `hsb2` data that only contained white students, we *would* use a text value, since data in the `race` variable are stored as character values:

```
* New dataset including only white students;
data hsb2_white;
	set hsb2;
	where race = "white";
run;
```

In this case, the text value in the `where` statement must match the values stored in the data exactly (i.e., same capitalization and, if applicable, spacing). This is another reason why checking the distribution of a character variable using `PROC FREQ` is helpful. The output shows you exactly what values appear in the dataset variable.

Now we will practice filtering and handling factors with a different categorical variable.

### Filtering based on a factor

Next, we'll practice working with a factor variable, `number`, from the `email50` dataset. This variable tells us what type of number (none, small, or big) an email contains.

How would we create a new dataset called `email50_big` that is a subset of the original `email50` dataset containing only emails with `"big"` numbers. This information is stored in the formatted `number` variable. 

```
* Check number distribution in email50;
proc freq data=email50;
	tables number / missing;
run;

* New dataset including only big number emails;
data email50_big;
	set email50;
	where number = 3;
run;
```

Does it make sense why we limited the new dataset to records where the `number` variable equaled `3`? How many records had "big" numbers?

You can always `%glimpse` the new dataset to set more detail about the new dataset:


```
%glimpse(email50_big)
```

## Creating new variables from existing variables 

Two common ways of creating a new variables from an existing variables are (1) *discretizing* numerical variable values and (b) *combining* categorical variable values.

Discretizing is the process of converting a numerical variable to a categorical variable based on certain criteria. For example, suppose we are not interested in the actual reading score of students, but instead whether their reading score is below average or at or above average. We could create a new variable based on the scores in `read` to achieve this.

Combining is the process of reducing the number of categories (values) in a categorical variable. For example, the `email50` dataset has a categorical variable called `number` with levels `"none"`, `"small"`, and `"big"`. But suppose we're only interested in whether an email contains a number. We would create a new variable that combines the levels `"some"` and `"big"` into one level named `"yes"`, and keeps the `"none"` level separate as a level named `"no"`. 

Neither of these processes will be covered here, since this is something covered in detail in PHS 703. If there is need to discretize or combine data in a 701 tutorial, we will give you the needed code.

## Visualizing numerical data

The most logical and most useful first step of any data analysis is an exploratory analysis. And a very important and informative component of exploratory data analysis is visualization.

We will learn a lot more about data visualization in the tutorial on Summarizing and Visualizing Data, so we won't go into too much detail on data visualization in this tutorial. Let's, however, make a simple scatterplot to visualize the relationship between two numerical variables so that you can get some exposure to constructing plots in SAS and how to interpret them.

There are many methods for visualizing data in SAS, but in this tutorial we will focus on using `PROC SGPLOT` . This is one of the newest SAS procedures for creating many different types of plots. Once you learn how to control the look and feel of graphs within `PROC SGPLOT`, it doesn't matter if you are creating a bar chart, a scatterplot, or a line graph. The concepts and syntax are largely reusable across figure types.

As an example, we'll visualize the relationship between the math and science scores of the students in the High School and Beyond dataset.

```
* Basic scatterplot of math v. science scores;
proc sgplot data=hsb2;
	scatter x=science y=math /
		markerattrs=(symbol=circlefilled)
	;
run;
```

Let's pause for a moment and review what's going on in the code above.

- We use the `SGPLOT` procedure to create plots using the `HSB2` dataset.
- We are requesting a scatterplot with to plot science test scores on the $x$-axis and math test scores on the $y$-axis.
- The `markerattrs` option isn't necessary, but using a different symbol helps us see the data points more clearly.

Now that you've learned how to make the plot, let's talk about what the plot says.

### Interpreting a visualization

If we add a regression line through these points, we can see that there is a positive relationship between the science and math scores of students, meaning that students who score highly in science tend to also score highly in math. Probably not that surprising a result.

```
* Scatterplot+regression line of math v. science scores;
proc sgplot data=hsb2;
	reg x=science y=math /
    	markerattrs=(symbol=circlefilled)
    ;
run;
```

We also mentioned earlier that extending from bivariate to multivariate plots is easy in ggplot2. Let's test that out!

### Math, science, and program

Let's plot the same math and science test scores, but this time let's also consider the program that the student is in: general, academic, or vocational:

```
* Scatterplot of math v. science scores, by program;
proc sgplot data=hsb2;
	scatter x=science y=math /
		group=prog markerattrs=(symbol=circlefilled)
	;
run;
```

The code looks very similar to the scatterplot code we used before, except that we added the `group=` option to color the points by program.

Look at the plot generated by the code above and think about where the lines would go if we fit a separate line for each level of the program variable (each color of points). The result would be a plot that looks like the one generated by the code below:

```
* Scatterplot+regression line of math v. science scores, by program;
proc sgplot data=hsb2;
	reg x=science y=math /
		group=prog markerattrs=(symbol=circlefilled)
	;
run;
```

The same positive relationship between math and science scores is still apparent. But we can also see that students in academic programs, shown with green points, tend to score higher than those in vocational programs, in blue, and general programs, in red.

We will wrap up this lesson with some practice creating a similar plot with different variables.

### Visualizing numerical and categorical data

Next we'll visualize the relationship between two numerical variables from the `email50` dataset, separated by whether or not the email was spam. This means that we will use an aspect of the plot (like color or shape) to identify the levels in the `spam` variable so that we can compare plotted values between them.

Let's create a scatterplot of number of exclamation points (`exclaim_mess`) on the y-axis vs. number of characters (`num_char`) on the x-axis, while coloring the points by whether or not the email is `spam`.

```
proc sgplot data=email50;
	scatter x=num_char y=exclaim_mess /
		group=spam markerattrs=(symbol=circlefilled)
	;
run;
```

*Based on the plot, does there appear to be a relationship between these variables?* 



You have successfully completed Lesson 1 in Tutorial 1: Getting Started with Data.

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

