## Reference cell coding

Categorical variables with greater than 2 levels require special handling in regression models. Unlike numerical variable or dichotomous variables, they cannot be entered into the regression model as is. Using the information in these categorical variables, we need to create multiple new variables for inclusion into the regression model . There are [many coding systems](https://stats.idre.ucla.edu/spss/faq/coding-systems-for-categorical-variables-in-regression-analysis/) available for doing this, but probably the most common one, and the one we'll talk about here is *reference cell coding*. The general idea is that we need to use *k* – 1 indicator variables (coded using 0s and 1s) to fully represent the information from a categorical variable with *k* levels.

The example below will use salary data from Virginia Beach Police Department, specifically for the following 4 non-management ranks of officers:

* Police Officer Recruit
* Police Officer I
* Police Officer II
* Master Police Officer

Let's load those data:

```
* Initialize SAS session;
%include "~/my_shared_file_links/hammi002/sasprog/run_first.sas";

* Make a working copy of VA_BEACH_OFFICERS;
%use_data(va_beach_officers);
%glimpse(va_beach_officers);
```

And let's look at the average salary within each of those ranks:

```
* Mean salary by position title (POSTITLE);
proc means data=va_beach_officers maxdec=0;
    var Salary;
    class PosTitle;
run;
```

The table below summarizes this `PROC MEANS` output and adds information about the difference in mean salary for each group compared to both the Police Officer Recruit and Master Police Officer mean salaries, which will important for us later:

| Rank            | Mean Salary | $$\Delta$$ vs Recruit | $$\Delta$$ vs Master |
| --------------- | ----------- | --------------------- | -------------------- |
| Officer Recruit | $45,728     | 0                     | –27,389              |
| Officer I       | $51,259     | +5,531                | –21,858              |
| Officer II      | $56,789     | +11,061               | –16,328              |
| Master Officer  | $73,117     | +27,389               | 0                    |

As noted above, if we want to model salary as a function of  rank using `PROC REG` in SAS, we will need to implement the reference cell coding above. 

To start, what if we create 4 individual 0/1 variables to represent each rank, like this:

* When `PosTitle` = "Police Officer Recruit", set `Officer_Recruit` = 1, otherwise set to 0
* When `PosTitle` = "Police Officer I", set `Officer_I` = 1, otherwise set to 0
* When `PosTitle` = "Police Officer II", set `Officer_II` = 1, otherwise set to 0
* When `PosTitle` = "Master Police Officer", set `Officer_Master` = 1, otherwise set to 0

For our data, the cross-tabulation of `PosTitle` with these new 4 variables looks like this:

| PosTitle               | Officer_Recruit | Officer_I | Officer_II | Officer_Master | N    |
| ---------------------- | --------------- | --------- | ---------- | -------------- | ---- |
| Police Officer Recruit | 1               | 0         | 0          | 0              | 47   |
| Police Officer I       | 0               | 1         | 0          | 0              | 100  |
| Police Officer II      | 0               | 0         | 1          | 0              | 148  |
| Master Police Officer  | 0               | 0         | 0          | 1              | 316  |

We can see in this table that each rank value in the raw variable, `PosTitle`, receives a value of 1 in only one of the other variables. You can confirm this yourself in the data:

```
* Verify coding into indicator variables;
proc freq data=va_beach_officers;
    tables PosTitle * Officer_Recruit * Officer_I * Officer_II * Officer_Master / list nocum;
run;
```

Now, if we try to include all 4 of these indicator variables into a regression model, SAS is going to complain. So let's just try:

```
* Wrong: Do not include all 4 indicator variables;
proc reg data=va_beach_officers;
	model salary = officer_recruit officer_i officer_ii officer_master;
run;
```

Aha. Notice that SAS gives us two messages:

> **Note**: Model is not full rank. ...

and

> **Note**: The following parameters have been set to 0, since the variables are a linear combination of other variables as shown.
>
> **Officer_Master** = Intercept - Officer_Recruit - Officer_I - Officer_II

Both mean that we have too many variables in the model. To fix this, SAS has decided to ignore the `Officer_Master`  variable. Can you see how the parameter estimate for that variable is set to 0?

This is a hint that the correct way reflect categorical variable with *k* levels is to include indicator variables for all but one (*k* – 1) of those values. The level of the categorical variable that is coded as zero in all of the included indicator variables is the *reference level*, or the level to which all of the other levels are compared. 

### Using Recruits as the reference

So let's use Recruits as the reference level and include the `officer_i`, `officer_ii`, `officer_master` variables in the model. You may find it helpful to write out the population regression model equation:

$$Y = \beta_0 + \beta_1 Officer\_I + \beta_2 Officer\_II + \beta_3 Officer\_Master + \epsilon$$

In SAS, this is:

```
* Using Recruits as the reference;
proc reg data=va_beach_officers;
	model salary = officer_i officer_ii officer_master;
run;
```

Look at this model output. First, SAS is no longer complaining. Second, if you compare the parameter estimates here to the table of mean salaries above, you should see the following things:

* The estimate of the intercept, $$b_0$$, equals the mean salary for the Recruits
* The estimate for the parameter associated with the Officer I level, $$b_1$$, equals the difference in mean salary between Officer I's and Recruits
* The estimate for the parameter associated with the Officer II level, $$b_2$$, equals the difference in mean salary between Officer II's and Recruits
* The estimate for the parameter associated with the Master Officer level, $$b_3$$, equals the difference in mean salary between Master Officers and Recruits

The interpretation of the intercept will vary from what's above if there are other variables in the model, but the slope estimates will *always* have the interpretation as the difference in the outcome between that specific level of the categorical variable compared to the reference level.

Of course, you can select any level of the categorical variable as the reference level. 

### Using Master Officers as the reference

Let's change the reference level, and use Master Officers as the reference level. Here, we will include the `officer_recruit`, `officer_i`, and `officer_ii` variables in the model. Again, let's write out the population regression model equation:

$$Y = \beta_0 + \beta_1 Officer\_Recruit + \beta_2 Officer\_I + \beta_3 Officer\_II + \epsilon$$

In SAS, this will be:

```
* Using Master Officers as the reference;
proc reg data=va_beach_officers;
	model salary = officer_recruit officer_i officer_ii;
run;
```

Look at this model output, and again compare the parameter estimates to the table of mean salaries above. You should see the following things:

* The estimate of the intercept, $$b_0$$, now equals the mean salary for the Master Officers
* The estimate for the parameter associated with the Recruit level, $$b_1$$, equals the difference in mean salary between Recruits and Master Officers
* The estimate for the parameter associated with the Officer I level, $$b_2$$, equals the difference in mean salary between Officer I's and Master Officers
* The estimate for the parameter associated with the Officer II level, $$b_3$$, equals the difference in mean salary between Officer II's and Master Officers

In other words, interpretation always reflects the choice of reference level. 

### Different numbers of levels

Reference cell coding is flexible for any number of levels of a categorical variable. For example:

* To include day of the week (Mon, Tue, Wed, etc.) in a regression model, you would need 6 indicator variables
* To include North Carolina counties (there are 100 of them) in a regression model, you would need 99 indicator variables
* Etc.

It is important to understand how to incorporate categorical variables into regression models and how to interpret the associated regression output.



You have successfully completed this tutorial.

# [< Back to Section 3](https://bghammill.github.io/ims-03-model/)





<!-- MathJax -->

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

