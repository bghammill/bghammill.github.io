For an exploration of some numeric data, we are going to use data from `gapminder`, which tracks demographic data in countries of the world over time. Let's load and take a look at those data:

```
* Makes a working copy of GAPMINDER data;
%use_data(gapminder);
%glimpse(gapminder);
```

If you look at the first few records of this dataset, you can see that there is information from multiple years for Afghanistan. This is true for all the countries. We're only interested in exploring the most recent data, so the first thing we need to do it limit this dataset to information from 2007 (the latest available from this source):

```
* Make dataset with 2007 data only;
data gap2007;
	set gapminder;
	where year = 2007;
run;

* Take a look at the new dataset;
%glimpse(gap2007);
```

*How many countries do we have 2007 data for?*

There are 3 numerical variables on this dataset that we will use at various points in this tutorial:

* `gdppercap`: GDP per capita (US$, inflation-adjusted)
* `lifexp`: Life expectancy at birth, in years
* `pop`: Population





### 