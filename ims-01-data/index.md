# Introduction to data

## Tutorial description

Scientists seek to answer questions using rigorous methods and careful observations. These observations - collected from the likes of field notes, surveys, and experiments - form the backbone of a statistical investigation and are called data. Statistics is the study of how best to collect, analyze, and draw conclusions from data. It is helpful to put statistics in the context of a general process of investigation:

1. Identify a question or problem.
2. Collect relevant data on the topic.
3. Analyze the data.
4. Form a conclusion.

In this tutorial, we focus on steps 1 and 2 of this process.

## Learning objectives

- Internalize the language of data.
- Load and view a dataset in R and distinguish between various variable types.
- Classify a study as observational or experimental, and determine whether the study’s results can be generalized to the population and whether they suggest correlation or causation between the variables studied.
- Distinguish between various sampling strategies and recognize the benefits and drawbacks of choosing one strategy over another.
- Identify the principles of experimental design and recognize their purposes.
- Apply terminology and principles to a case study.

## Lessons

### 1 - [Language of data](https://bghammill.github.io/ims-01-model/ims-01-lesson-01/)

- Load data from the textbook companion package
- Introduce data frames and tidy data
- Discuss variable types connecting terminology from textbook to R
- Mutate data frames to convert data types
  - Numerical to categorical conversion
  - Filtering and then drop levels
  - Combine levels of categorical
  - Create new variable based on two existing variables (e.g. BMI)

### 2 - [Types of studies](https://bghammill.github.io/ims-01-model/ims-01-lesson-02/)

- Define observational studies and experiments
- Discuss scope of inference 2x2 grid with random assignment and sampling
- Define Simpson’s paradox in a 2 cat var case
  - Use R to make a contingency table
  - `group_by()` third variable and make table again to demonstrate Simpson’s paradox

### 3 - [Sampling strategies and experimental design](https://bghammill.github.io/ims-01-model/ims-01-lesson-03/)

- Define simple random sample, stratified sample, cluster sample, multistage sample
- Use R to obtain SRS and stratified sample
  - `slice_sample()`
  - `group_by()` %>% `slice_sample()`
- Discuss benefits and drawbacks of choosing one sampling scheme over another
- Identify the principles of experimental design
- Discuss the purpose of each principle
- Use R to do random assignment and random assignment after blocking
  - `slice_sample()`
  - `group_by()` %>% `slice_sample()`

### 4 - [Case study](https://bghammill.github.io/ims-01-model/ims-01-lesson-04/)

- Apply terminology, principles, and R code learned in this tutorial to a case study

