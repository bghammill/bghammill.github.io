# Exploratory data analysis

## Tutorial description

When your dataset is represented as a table or a database, it’s difficult to observe much about it beyond its size and the types of variables it contains. In this tutorial, you’ll learn how to use graphical and numerical techniques to begin uncovering the structure of your data. Which variables suggest interesting relationships? Which observations are unusual? By the end of the tutorial, you’ll be able to answer these questions and more, while generating graphics that are both insightful and beautiful.

## Learning objectives

- Visualize categorical and numerical data using appropriate graphics.
- Create graphical representations of multiple variables.
- Describe the structure revealed by graphics in the language of distributions.
- Use statistics to summarize important aspects of data.
- Identify unusual observations

## Lessons

### 1 - [Visualizing categorical data](https://bghammill.github.io/ims-02-explore/ims-02-lesson-01/)

Creating graphical and numerical summaries of two categorical variables, primarily using two SAS procedures: `PROC FREQ` and `PROC  SGPLOT`.

- Graphical representation two categorical variables
  - Side-by-side bar charts
  - Stacked bar chars
  - To normalize or not to normalize
- Tabular representation of two categorical variables
  - Computation of margins
  - Counts vs proportions
  - Law of total probability
- Graphical representation of one categorical variable
  - Marginal vs conditional
  - Bar chart
    - ordering
    - data integrity check for levels
  - Pie chart

### 2 - [Visualizing numerical data](https://bghammill.github.io/ims-02-explore/ims-02-lesson-02/)

Learn useful statistics for describing distributions of numerical data.

- Graphical representation of one categorical and one numerical variable
- Side-by-side boxplots
- Faceted histograms
- Colored density curves
- Graphical representation of one numerical variable
- Marginal vs conditioning
- Histogram
- binwidth
- Density plot
- bandwidth
- Boxplot
- outlier detection

### 3 - [Summarizing with statistics](https://bghammill.github.io/ims-02-explore/ims-02-lesson-03/)

Statistics for describing distributions of data.

- Center: mean, median, mode
- Shape: skewness, modality
- Spread: range, IQR, SD, variance
- Unusual observations
- Transformations: Logarithm and sqrt to reduce skew in graphics and ease comparisons.

### 4 - [Case study](https://bghammill.github.io/ims-02-explore/ims-02-lesson-04/)

Apply what you’ve learned to explore and summarize a real world dataset in this case study of email spam.

