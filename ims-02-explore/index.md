# Exploratory data analysis

When your data sits in a SAS dataset or other database, it’s difficult to observe much about it beyond its size and the types of variables it contains. In this tutorial, we'll learn about summarizing and visualizing your data.  Sometimes, we refer to the techniques covered in this tutorial as **exploratory data analysis** or EDA. 

EDA is all about learning the structure of a dataset through a series of numerical and graphical techniques. When you do EDA, you'll generate summaries of your data that help you understand the distributions of variables, uncover relationship, and find outliers. EDA can also generate questions that will help inform subsequent analysis.

## Learning objectives

- Visualize categorical and numerical data using appropriate graphics.
- Create graphical representations of multiple variables.
- Describe the structure revealed by graphics in the language of distributions.
- Use statistics to summarize important aspects of data.
- Identify unusual observations

## Lessons

### [Exploring single variables](https://bghammill.github.io/ims-02-explore/ims-02-lesson-05/)

Create graphical and numerical summaries to understand the marginal distributions of single variables

* Categorical variables
  * Frequencies & relative frequency tables
  * Bar charts and pie charts
* Numerical variables
  * Measures of center
  * Measures of spread (variability)
  * Histograms and box plots

### [Exploring relationships between two variables](https://bghammill.github.io/ims-02-explore/ims-02-lesson-06/)

Create graphics that display and calculate numeric measures that summarize the relationship between multiple variables  

* Categorical variable x categorical variable
  * Contingency tables
  * Segmented bar charts and mosaic plots
  * Relative risk & risk difference
* Numerical variable x numerical variable
  * Scatterplot
  * Correlation coefficient
* Categorical variable x numerical variable
  * Paneled histograms & box plots
  * Mean differences

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



# [< Back to main page](https://bghammill.github.io/)

