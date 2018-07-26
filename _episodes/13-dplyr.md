---
title: Dataframe Manipulation with dplyr
teaching: 40
exercises: 15
questions:
- "How can I manipulate dataframes without repeating myself?"
objectives:
- " To be able to use the six main dataframe manipulation 'verbs' with pipes in  `dplyr`."
- " To understand how `group_by()` and `summarise()` can be combined to summarise datasets."
- " Be able to analyze a subset of data using logical filtering."
- " Be able to join different dataframes based on common variables"
keypoints:
- "Use the `dplyr` package to manipulate dataframes."
- "Use `select()` to choose variables from a dataframe."
- "Use `filter()` to choose data based on values."
- "Use `group_by()` and `summarise()` to work with subsets of data."
- "Use `mutate()` to create new variables."
- "Join dataframes with `left_join()`, `full_join()`, inner_join()` or find non-matching rows with `anti_join()`"
source: Rmd
---



Manipulation of dataframes means many things to many researchers, we often
select certain observations (rows) or variables (columns), we often group the
data by a certain variable(s), or we even calculate summary statistics. We can
do these operations using the normal base R operations:


~~~
mean(gapminder[gapminder$continent == "Africa", "gdpPercap"])
~~~
{: .language-r}



~~~
[1] 2193.755
~~~
{: .output}



~~~
mean(gapminder[gapminder$continent == "Americas", "gdpPercap"])
~~~
{: .language-r}



~~~
[1] 7136.11
~~~
{: .output}



~~~
mean(gapminder[gapminder$continent == "Asia", "gdpPercap"])
~~~
{: .language-r}



~~~
[1] 7902.15
~~~
{: .output}

But this isn't very *nice* because there is a fair bit of repetition. Repeating
yourself will cost you time, both now and later, and potentially introduce some
nasty bugs.

## The `dplyr` package

Luckily, the [`dplyr`](https://cran.r-project.org/web/packages/dplyr/dplyr.pdf)
package provides a number of very useful functions for manipulating dataframes
in a way that will reduce the above repetition, reduce the probability of making
errors, and probably even save you some typing. As an added bonus, you might
even find the `dplyr` grammar easier to read.

Here we're going to cover 6 of the most commonly used functions as well as using
pipes (`%>%`) to combine them.

1. `select()`
2. `filter()`
3. `group_by()`
4. `summarise()`
5. `mutate()`

If you have have not installed this package earlier, please do so:


~~~
install.packages('dplyr')
~~~
{: .language-r}

Now let's load the package:


~~~
library("dplyr")
~~~
{: .language-r}

### A brief detour to talk tibbles

As we saw previously, dataframes are the engine of R's power. The `dplyr` package introduces a new and improved version of the dataframe - the tibble. In effect, dataframes and tibbles are interchangeable - tibbles keep everything that works about dataframes, and adds a few nifty features.

You can convert a dataframe to a tibble by using `as_tibble()`, or you can use another package `readr`, which provides a `read_csv()` function that returns a tibble.

~~~
install.packages('readr')
~~~
{: .language-r}

We can now load the package and use `read_csv` to read the data into R as a tibble.

~~~
library(readr)
gapminder <- read_csv("data/gapminder-FiveYearData.csv")
~~~
{: .language-r}



~~~
Parsed with column specification:
cols(
  country = col_character(),
  year = col_integer(),
  pop = col_double(),
  continent = col_character(),
  lifeExp = col_double(),
  gdpPercap = col_double()
)
~~~
{: .output}



~~~
gapminder
~~~
{: .language-r}



~~~
# A tibble: 1,704 x 6
   country      year       pop continent lifeExp gdpPercap
   <chr>       <int>     <dbl> <chr>       <dbl>     <dbl>
 1 Afghanistan  1952  8425333. Asia         28.8      779.
 2 Afghanistan  1957  9240934. Asia         30.3      821.
 3 Afghanistan  1962 10267083. Asia         32.0      853.
 4 Afghanistan  1967 11537966. Asia         34.0      836.
 5 Afghanistan  1972 13079460. Asia         36.1      740.
 6 Afghanistan  1977 14880372. Asia         38.4      786.
 7 Afghanistan  1982 12881816. Asia         39.9      978.
 8 Afghanistan  1987 13867957. Asia         40.8      852.
 9 Afghanistan  1992 16317921. Asia         41.7      649.
10 Afghanistan  1997 22227415. Asia         41.8      635.
# ... with 1,694 more rows
~~~
{: .output}

You can see that when we print gapminder now, instead of getting an unwieldy amount of output, we get a manageable preview, as well as some really useful information.

> ## Discussion 1
>
> What additional information does the `tibble` give us?
> What functions would we need to use to get the same information from a traditional dataframe?
> What do you notice about the country and continent variables that is different from using `read.csv()`?
>
> > ## Discussion 1
> >
> > The tibble lets us see what the dimensions are (rows and columns). It also shows what the type of each column is. If there were more columns, it would helpfully trim each column so that more fit in the view as well.
> {: .solution}
{: .discussion}

## Using `select()`

If, for example, we wanted to move forward with only a few of the variables in
our dataframe we could use the `select()` function. This will keep only the
variables you select.


~~~
year_country_gdp <- select(gapminder,year,country,gdpPercap)
~~~
{: .language-r}

![](../fig/13-dplyr-fig1.png)

If we open up `year_country_gdp` we'll see that it only contains the year,
country and gdpPercap. 

### Pipes in R

Above we used 'normal' grammar, but the strengths of
`dplyr` lie in combining several functions using pipes. 

`dplyr` is arranged around verbs (select, filter, mutate, summarise), and pipes let us build up 'paragraphs'.

Since the pipes grammar is unlike anything we've seen in R before, let's repeat what we've done above using pipes.


~~~
year_country_gdp <- gapminder %>% select(year,country,gdpPercap)
~~~
{: .language-r}

To help you understand why we wrote that in that way, let's walk through it step
by step. First we summon the gapminder dataframe and pass it on, using the pipe
symbol `%>%`, to the next step, which is the `select()` function. In this case
we don't specify which data object we use in the `select()` function since in
gets that from the previous pipe. 

**Fun Fact**: The pipe in this context is very similar to what we saw in the shell. In R, a pipe symbol is `%>%` while in the shell it is `|` but the concept is the same! In R, we can't use `|` because it already has meaning (the logical "or").

### More powerful selections

We can do more than just select by typing names. You can use column indices:


~~~
gapminder %>% 
  select(1:3)
~~~
{: .language-r}



~~~
# A tibble: 1,704 x 3
   country      year       pop
   <chr>       <int>     <dbl>
 1 Afghanistan  1952  8425333.
 2 Afghanistan  1957  9240934.
 3 Afghanistan  1962 10267083.
 4 Afghanistan  1967 11537966.
 5 Afghanistan  1972 13079460.
 6 Afghanistan  1977 14880372.
 7 Afghanistan  1982 12881816.
 8 Afghanistan  1987 13867957.
 9 Afghanistan  1992 16317921.
10 Afghanistan  1997 22227415.
# ... with 1,694 more rows
~~~
{: .output}

Additionally, you can select based on the names of columns. `starts_with()`, `ends_with()` and `contains()` do exactly what they sound like. 

> ## Challenge 1
>
> Select all columns from `gapminder` that start with "co"
>
> > ## Solution to Challenge 1
> >
> >~~~
> >gapminder %>%
> >  select(starts_with("co"))
> >~~~
> >{: .language-r}
> >
> >
> >
> >~~~
> ># A tibble: 1,704 x 2
> >   country     continent
> >   <chr>       <chr>    
> > 1 Afghanistan Asia     
> > 2 Afghanistan Asia     
> > 3 Afghanistan Asia     
> > 4 Afghanistan Asia     
> > 5 Afghanistan Asia     
> > 6 Afghanistan Asia     
> > 7 Afghanistan Asia     
> > 8 Afghanistan Asia     
> > 9 Afghanistan Asia     
> >10 Afghanistan Asia     
> ># ... with 1,694 more rows
> >~~~
> >{: .output}
> {: .solution}
{: .challenge}

> ## Challenge 2
>
> Select all columns from `gapminder` that contain the letter "p"
>
> > ## Solution to Challenge 2
> >
> >~~~
> >gapminder %>%
> >  select(contains("p"))
> >~~~
> >{: .language-r}
> >
> >
> >
> >~~~
> ># A tibble: 1,704 x 3
> >         pop lifeExp gdpPercap
> >       <dbl>   <dbl>     <dbl>
> > 1  8425333.    28.8      779.
> > 2  9240934.    30.3      821.
> > 3 10267083.    32.0      853.
> > 4 11537966.    34.0      836.
> > 5 13079460.    36.1      740.
> > 6 14880372.    38.4      786.
> > 7 12881816.    39.9      978.
> > 8 13867957.    40.8      852.
> > 9 16317921.    41.7      649.
> >10 22227415.    41.8      635.
> ># ... with 1,694 more rows
> >~~~
> >{: .output}
> {: .solution}
{: .challenge}


## Using `filter()`

If we now wanted to move forward with the above, but only with European
countries, we can combine `select` and `filter`


~~~
year_country_gdp_euro <- gapminder %>%
    filter(continent=="Europe") %>%
    select(year,country,gdpPercap)
~~~
{: .language-r}

> ## Challenge 3
>
> Write a single command (which can span multiple lines and includes pipes) that
> will produce a dataframe that has the African values for `lifeExp`, `country`
> and `year`, but not for other Continents.  How many rows does your dataframe
> have and why?
>
> > ## Solution to Challenge 3
> >
> >~~~
> >year_country_lifeExp_Africa <- gapminder %>%
> >                            filter(continent=="Africa") %>%
> >                            select(year,country,lifeExp)
> >~~~
> >{: .language-r}
> {: .solution}
{: .challenge}

As with last time, first we pass the gapminder dataframe to the `filter()`
function, then we pass the filtered version of the gapminder dataframe to the
`select()` function. **Note:** The order of operations is very important in this
case. If we used 'select' first, filter would not be able to find the variable
continent since we would have removed it in the previous step.

> ## Challenge 4
>
> Write a single command that will produce a dataframe that only has years
> after 1975, and records where life expectancy is less than 50.
> > ## Solution to Challenge 4
> >
> >~~~
> >lifeExp_50_after_1975 <- gapminder %>% 
> >                           filter(year > 1975, lifeExp < 50)
> >~~~
> >{: .language-r}
> {: .solution}
{: .challenge}

## Using `arrange()`

The `arrange()` verb does exactly what it says - arrange rows according to the values in variables. To define which variables are to be used, they are supplied to arrange in order or priority. By default, the values are arranged in ascending order. To arrange by descending order, wrap a variable name in `desc()`.

> ## Challenge 5
>
> Find the record with the lowest population
> > ## Solution to Challenge 5
> >
> >~~~
> >gapminder %>% 
> >   arrange(pop)
> >~~~
> >{: .language-r}
> >
> >
> >
> >~~~
> ># A tibble: 1,704 x 6
> >   country                year    pop continent lifeExp gdpPercap
> >   <chr>                 <int>  <dbl> <chr>       <dbl>     <dbl>
> > 1 Sao Tome and Principe  1952 60011. Africa       46.5      880.
> > 2 Sao Tome and Principe  1957 61325. Africa       48.9      861.
> > 3 Djibouti               1952 63149. Africa       34.8     2670.
> > 4 Sao Tome and Principe  1962 65345. Africa       51.9     1072.
> > 5 Sao Tome and Principe  1967 70787. Africa       54.4     1385.
> > 6 Djibouti               1957 71851. Africa       37.3     2865.
> > 7 Sao Tome and Principe  1972 76595. Africa       56.5     1533.
> > 8 Sao Tome and Principe  1977 86796. Africa       58.6     1738.
> > 9 Djibouti               1962 89898. Africa       39.7     3021.
> >10 Sao Tome and Principe  1982 98593. Africa       60.4     1890.
> ># ... with 1,694 more rows
> >~~~
> >{: .output}
> {: .solution}
{: .challenge}

> ## Challenge 6
> 
> Find the country with the lowest population after 1982 that also had a 
> life expectancy greater than 70
> > ## Solution to Challenge 6
> >
> >~~~
> >gapminder %>% 
> >   filter(year == 1982, lifeExp > 70) %>% 
> >   arrange(pop)
> >~~~
> >{: .language-r}
> >
> >
> >
> >~~~
> ># A tibble: 44 x 6
> >   country     year      pop continent lifeExp gdpPercap
> >   <chr>      <int>    <dbl> <chr>       <dbl>     <dbl>
> > 1 Iceland     1982  233997. Europe       77.0    23270.
> > 2 Montenegro  1982  562548. Europe       74.1    11223.
> > 3 Kuwait      1982 1497494. Asia         71.3    31354.
> > 4 Slovenia    1982 1861252. Europe       71.1    17867.
> > 5 Panama      1982 2036305. Americas     70.5     7010.
> > 6 Jamaica     1982 2298309. Americas     71.2     6068.
> > 7 Costa Rica  1982 2424367. Americas     73.4     5263.
> > 8 Singapore   1982 2651869. Asia         71.8    15169.
> > 9 Albania     1982 2780097. Europe       70.4     3631.
> >10 Uruguay     1982 2953997. Americas     70.8     6920.
> ># ... with 34 more rows
> >~~~
> >{: .output}
> {: .solution}
{: .challenge}

## Using `group_by()` and `summarise()`

Now, we were supposed to be reducing the error prone repetitiveness of what can
be done with base R, but up to now we haven't done that since we would have to
repeat the above for each continent. Instead of `filter()`, which will only pass
observations that meet your criteria (in the above: `continent=="Europe"`), we
can use `group_by()`, which will essentially use every unique criteria that you
could have used in filter.


~~~
str(gapminder)
~~~
{: .language-r}



~~~
Classes 'tbl_df', 'tbl' and 'data.frame':	1704 obs. of  6 variables:
 $ country  : chr  "Afghanistan" "Afghanistan" "Afghanistan" "Afghanistan" ...
 $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
 $ pop      : num  8425333 9240934 10267083 11537966 13079460 ...
 $ continent: chr  "Asia" "Asia" "Asia" "Asia" ...
 $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
 $ gdpPercap: num  779 821 853 836 740 ...
 - attr(*, "spec")=List of 2
  ..$ cols   :List of 6
  .. ..$ country  : list()
  .. .. ..- attr(*, "class")= chr  "collector_character" "collector"
  .. ..$ year     : list()
  .. .. ..- attr(*, "class")= chr  "collector_integer" "collector"
  .. ..$ pop      : list()
  .. .. ..- attr(*, "class")= chr  "collector_double" "collector"
  .. ..$ continent: list()
  .. .. ..- attr(*, "class")= chr  "collector_character" "collector"
  .. ..$ lifeExp  : list()
  .. .. ..- attr(*, "class")= chr  "collector_double" "collector"
  .. ..$ gdpPercap: list()
  .. .. ..- attr(*, "class")= chr  "collector_double" "collector"
  ..$ default: list()
  .. ..- attr(*, "class")= chr  "collector_guess" "collector"
  ..- attr(*, "class")= chr "col_spec"
~~~
{: .output}



~~~
str(gapminder %>% group_by(continent))
~~~
{: .language-r}



~~~
Classes 'grouped_df', 'tbl_df', 'tbl' and 'data.frame':	1704 obs. of  6 variables:
 $ country  : chr  "Afghanistan" "Afghanistan" "Afghanistan" "Afghanistan" ...
 $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
 $ pop      : num  8425333 9240934 10267083 11537966 13079460 ...
 $ continent: chr  "Asia" "Asia" "Asia" "Asia" ...
 $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
 $ gdpPercap: num  779 821 853 836 740 ...
 - attr(*, "spec")=List of 2
  ..$ cols   :List of 6
  .. ..$ country  : list()
  .. .. ..- attr(*, "class")= chr  "collector_character" "collector"
  .. ..$ year     : list()
  .. .. ..- attr(*, "class")= chr  "collector_integer" "collector"
  .. ..$ pop      : list()
  .. .. ..- attr(*, "class")= chr  "collector_double" "collector"
  .. ..$ continent: list()
  .. .. ..- attr(*, "class")= chr  "collector_character" "collector"
  .. ..$ lifeExp  : list()
  .. .. ..- attr(*, "class")= chr  "collector_double" "collector"
  .. ..$ gdpPercap: list()
  .. .. ..- attr(*, "class")= chr  "collector_double" "collector"
  ..$ default: list()
  .. ..- attr(*, "class")= chr  "collector_guess" "collector"
  ..- attr(*, "class")= chr "col_spec"
 - attr(*, "vars")= chr "continent"
 - attr(*, "drop")= logi TRUE
 - attr(*, "indices")=List of 5
  ..$ : int  24 25 26 27 28 29 30 31 32 33 ...
  ..$ : int  48 49 50 51 52 53 54 55 56 57 ...
  ..$ : int  0 1 2 3 4 5 6 7 8 9 ...
  ..$ : int  12 13 14 15 16 17 18 19 20 21 ...
  ..$ : int  60 61 62 63 64 65 66 67 68 69 ...
 - attr(*, "group_sizes")= int  624 300 396 360 24
 - attr(*, "biggest_group_size")= int 624
 - attr(*, "labels")='data.frame':	5 obs. of  1 variable:
  ..$ continent: chr  "Africa" "Americas" "Asia" "Europe" ...
  ..- attr(*, "spec")=List of 2
  .. ..$ cols   :List of 6
  .. .. ..$ country  : list()
  .. .. .. ..- attr(*, "class")= chr  "collector_character" "collector"
  .. .. ..$ year     : list()
  .. .. .. ..- attr(*, "class")= chr  "collector_integer" "collector"
  .. .. ..$ pop      : list()
  .. .. .. ..- attr(*, "class")= chr  "collector_double" "collector"
  .. .. ..$ continent: list()
  .. .. .. ..- attr(*, "class")= chr  "collector_character" "collector"
  .. .. ..$ lifeExp  : list()
  .. .. .. ..- attr(*, "class")= chr  "collector_double" "collector"
  .. .. ..$ gdpPercap: list()
  .. .. .. ..- attr(*, "class")= chr  "collector_double" "collector"
  .. ..$ default: list()
  .. .. ..- attr(*, "class")= chr  "collector_guess" "collector"
  .. ..- attr(*, "class")= chr "col_spec"
  ..- attr(*, "vars")= chr "continent"
  ..- attr(*, "drop")= logi TRUE
~~~
{: .output}
You will notice that the structure of the dataframe where we used `group_by()`
(`grouped_df`) is not the same as the original `gapminder` (`data.frame`). A
`grouped_df` can be thought of as a `list` where each item in the `list`is a
`data.frame` which contains only the rows that correspond to the a particular
value `continent` (at least in the example above).

![](../fig/13-dplyr-fig2.png)

## Using `summarise()`

The above was a bit on the uneventful side but `group_by()` is much more
exciting in conjunction with `summarise()`. This will allow us to create new
variable(s) by using functions that repeat for each of the continent-specific
data frames. That is to say, using the `group_by()` function, we split our
original dataframe into multiple pieces, then we can run functions
(e.g. `mean()` or `sd()`) within `summarise()`.


~~~
gdp_bycontinents <- gapminder %>%
    group_by(continent) %>%
    summarise(mean_gdpPercap=mean(gdpPercap))
~~~
{: .language-r}

![](../fig/13-dplyr-fig3.png)


~~~
continent mean_gdpPercap
     <fctr>          <dbl>
1    Africa       2193.755
2  Americas       7136.110
3      Asia       7902.150
4    Europe      14469.476
5   Oceania      18621.609
~~~
{: .language-r}

That allowed us to calculate the mean gdpPercap for each continent, but it gets
even better.

> ## Challenge 7
>
>
> Calculate the average life expectancy per country. Which has the longest average life
> expectancy and which has the shortest average life expectancy?
>
> > ## Solution to Challenge 7
> >
> >~~~
> >lifeExp_bycountry <- gapminder %>%
> >    group_by(country) %>%
> >    summarise(mean_lifeExp=mean(lifeExp))
> >lifeExp_bycountry %>%
> >    arrange(mean_lifeExp)
> >~~~
> >{: .language-r}
> >
> >
> >
> >~~~
> ># A tibble: 142 x 2
> >   country           mean_lifeExp
> >   <chr>                    <dbl>
> > 1 Sierra Leone              36.8
> > 2 Afghanistan               37.5
> > 3 Angola                    37.9
> > 4 Guinea-Bissau             39.2
> > 5 Mozambique                40.4
> > 6 Somalia                   41.0
> > 7 Rwanda                    41.5
> > 8 Liberia                   42.5
> > 9 Equatorial Guinea         43.0
> >10 Guinea                    43.2
> ># ... with 132 more rows
> >~~~
> >{: .output}
> >
> >
> >
> >~~~
> >lifeExp_bycountry %>%
> >    arrange(desc(mean_lifeExp))
> >~~~
> >{: .language-r}
> >
> >
> >
> >~~~
> ># A tibble: 142 x 2
> >   country     mean_lifeExp
> >   <chr>              <dbl>
> > 1 Iceland             76.5
> > 2 Sweden              76.2
> > 3 Norway              75.8
> > 4 Netherlands         75.6
> > 5 Switzerland         75.6
> > 6 Canada              74.9
> > 7 Japan               74.8
> > 8 Australia           74.7
> > 9 Denmark             74.4
> >10 France              74.3
> ># ... with 132 more rows
> >~~~
> >{: .output}
> {: .solution}
{: .challenge}

The function `group_by()` allows us to group by multiple variables. Let's group by `year` and `continent`.



~~~
gdp_bycontinents_byyear <- gapminder %>%
    group_by(continent,year) %>%
    summarise(mean_gdpPercap=mean(gdpPercap))
~~~
{: .language-r}

That is already quite powerful, but it gets even better! You're not limited to defining 1 new variable in `summarise()`.


~~~
gdp_pop_bycontinents_byyear <- gapminder %>%
    group_by(continent,year) %>%
    summarise(mean_gdpPercap=mean(gdpPercap),
              sd_gdpPercap=sd(gdpPercap),
              mean_pop=mean(pop),
              sd_pop=sd(pop))
~~~
{: .language-r}

## `count()` and `n()`

A very common operation is to count the number of observations for each
group. The `dplyr` package comes with two related functions that help with this.

For instance, if we wanted to check the number of countries included in the
dataset for the year 2002, we can use the `count()` function. It takes the name
of one or more columns that contain the groups we are interested in, and we can
optionally sort the results in descending order by adding `sort=TRUE`:


~~~
gapminder %>%
    filter(year == 2002) %>%
    count(continent, sort = TRUE)
~~~
{: .language-r}



~~~
# A tibble: 5 x 2
  continent     n
  <chr>     <int>
1 Africa       52
2 Asia         33
3 Europe       30
4 Americas     25
5 Oceania       2
~~~
{: .output}

If we need to use the number of observations in calculations, the `n()` function
is useful. For instance, if we wanted to get the standard error of the life
expectancy per continent:


~~~
gapminder %>%
    group_by(continent) %>%
    summarise(se_pop = sd(lifeExp)/sqrt(n()))
~~~
{: .language-r}



~~~
# A tibble: 5 x 2
  continent se_pop
  <chr>      <dbl>
1 Africa     0.366
2 Americas   0.540
3 Asia       0.596
4 Europe     0.286
5 Oceania    0.775
~~~
{: .output}

You can also chain together several summary operations; in this case calculating the `minimum`, `maximum`, `mean` and `se` of each continent's per-country life-expectancy:


~~~
gapminder %>%
    group_by(continent) %>%
    summarise(
      mean_le = mean(lifeExp),
      min_le = min(lifeExp),
      max_le = max(lifeExp),
      se_le = sd(lifeExp)/sqrt(n()))
~~~
{: .language-r}



~~~
# A tibble: 5 x 5
  continent mean_le min_le max_le se_le
  <chr>       <dbl>  <dbl>  <dbl> <dbl>
1 Africa       48.9   23.6   76.4 0.366
2 Americas     64.7   37.6   80.7 0.540
3 Asia         60.1   28.8   82.6 0.596
4 Europe       71.9   43.6   81.8 0.286
5 Oceania      74.3   69.1   81.2 0.775
~~~
{: .output}

## Using `mutate()`

We can also create new variables prior to (or even after) summarizing information using `mutate()`.


~~~
gdp_pop_bycontinents_byyear <- gapminder %>%
    mutate(gdp_billion = gdpPercap*pop/10^9) %>%
    group_by(continent, year) %>%
    summarise(mean_gdpPercap = mean(gdpPercap),
              sd_gdpPercap = sd(gdpPercap),
              mean_pop = mean(pop),
              sd_pop = sd(pop),
              mean_gdp_billion = mean(gdp_billion),
              sd_gdp_billion = sd(gdp_billion))
~~~
{: .language-r}

## Connect mutate with logical filtering: `case_when`

When creating new variables, we can hook this with a logical condition. A simple combination of
`mutate()` and `case_when()` facilitates filtering right where it is needed: in the moment of creating something new.
This easy-to-read statement is a fast and powerful way of depending on particular  conditions.


~~~
## Classifying data into groups depending on life expectancy
# Make categories based on life expectancy < 40, between 40 and 65, and > 65
lifeExp_categories <- gapminder %>%
    mutate(exp_cat = case_when(
      lifeExp < 40 ~ "less than 40",
      lifeExp >=40 & lifeExp < 65 ~ "between 40 and 65",
      lifeExp >=65 ~ "above 65"
      )) %>%
    group_by(continent, year) %>%
    count(exp_cat)

lifeExp_categories
~~~
{: .language-r}



~~~
# A tibble: 119 x 4
# Groups:   continent, year [60]
   continent  year exp_cat               n
   <chr>     <int> <chr>             <int>
 1 Africa     1952 between 40 and 65    23
 2 Africa     1952 less than 40         29
 3 Africa     1957 between 40 and 65    29
 4 Africa     1957 less than 40         23
 5 Africa     1962 between 40 and 65    37
 6 Africa     1962 less than 40         15
 7 Africa     1967 between 40 and 65    42
 8 Africa     1967 less than 40         10
 9 Africa     1972 between 40 and 65    46
10 Africa     1972 less than 40          6
# ... with 109 more rows
~~~
{: .output}

## Joining dataframes

It is extremely common that we have relevant data in more than one dataset. The dplyr package offers some very useful join functions to combine dataframes by common variables.

These functions take two dataframes and combine them based on matching values in common variables. 

`left_join()` keeps rows from the first dataframe, and adds matching values from the right dataframe.
`right_join()` does the reverse.
`inner_join()` only keeps rows that match between the two dataframes.
`full_join()` keeps all rows from both dataframes.
`anti_join()` only keeps rows from the first dataframe that *don't* match the second.

Let's make a couple of simple data.frames to demonstrate how a join works.


~~~
df1 <- data_frame(sample = c(1, 2, 3),
                  measure1 = c(4.2, 5.3, 6.1))
df2 <- data_frame(sample = c(1, 3, 4),
                  measure2 = c(7.8, 6.4, 9.0))
~~~
{: .language-r}

Now we can use each of the joins on these two data.frames:


~~~
left_join(df1, df2)
~~~
{: .language-r}



~~~
Joining, by = "sample"
~~~
{: .output}



~~~
# A tibble: 3 x 3
  sample measure1 measure2
   <dbl>    <dbl>    <dbl>
1     1.     4.20     7.80
2     2.     5.30    NA   
3     3.     6.10     6.40
~~~
{: .output}


~~~
right_join(df1, df2)
~~~
{: .language-r}



~~~
Joining, by = "sample"
~~~
{: .output}



~~~
# A tibble: 3 x 3
  sample measure1 measure2
   <dbl>    <dbl>    <dbl>
1     1.     4.20     7.80
2     3.     6.10     6.40
3     4.    NA        9.00
~~~
{: .output}


~~~
full_join(df1, df2)
~~~
{: .language-r}



~~~
Joining, by = "sample"
~~~
{: .output}



~~~
# A tibble: 4 x 3
  sample measure1 measure2
   <dbl>    <dbl>    <dbl>
1     1.     4.20     7.80
2     2.     5.30    NA   
3     3.     6.10     6.40
4     4.    NA        9.00
~~~
{: .output}


~~~
anti_join(df1, df2)
~~~
{: .language-r}



~~~
Joining, by = "sample"
~~~
{: .output}



~~~
# A tibble: 1 x 2
  sample measure1
   <dbl>    <dbl>
1     2.     5.30
~~~
{: .output}

For a more realistic example, let's use gapminder again.

Download the `gapminder_sex_ratios.csv` data from [here]({{ page.root }}/data/gapminder_sex_ratios.csv), and save it under our `/data` directory. Let's read that in and join it to our existing data.


~~~
gapminder_sr <- read_csv("data/gapminder_sex_ratios.csv")
~~~
{: .language-r}



~~~
Parsed with column specification:
cols(
  country = col_character(),
  year = col_integer(),
  sex_ratio = col_double()
)
~~~
{: .output}



~~~
gapminder_sr
~~~
{: .language-r}



~~~
# A tibble: 1,722 x 3
   country     year sex_ratio
   <chr>      <int>     <dbl>
 1 Burundi     1952      91.9
 2 Comoros     1952      98.8
 3 Djibouti    1952      98.6
 4 Eritrea     1952      98.2
 5 Ethiopia    1952      98.6
 6 Kenya       1952     102. 
 7 Madagascar  1952     106. 
 8 Malawi      1952      92.3
 9 Mauritius   1952      99.2
10 Mozambique  1952      95.6
# ... with 1,712 more rows
~~~
{: .output}



~~~
gapminder_left_join <- left_join(gapminder, gapminder_sr)
~~~
{: .language-r}



~~~
Joining, by = c("country", "year")
~~~
{: .output}



~~~
gapminder_left_join
~~~
{: .language-r}



~~~
# A tibble: 1,704 x 7
   country      year       pop continent lifeExp gdpPercap sex_ratio
   <chr>       <int>     <dbl> <chr>       <dbl>     <dbl>     <dbl>
 1 Afghanistan  1952  8425333. Asia         28.8      779.      112.
 2 Afghanistan  1957  9240934. Asia         30.3      821.      109.
 3 Afghanistan  1962 10267083. Asia         32.0      853.      107.
 4 Afghanistan  1967 11537966. Asia         34.0      836.      105.
 5 Afghanistan  1972 13079460. Asia         36.1      740.      104.
 6 Afghanistan  1977 14880372. Asia         38.4      786.      103.
 7 Afghanistan  1982 12881816. Asia         39.9      978.      104.
 8 Afghanistan  1987 13867957. Asia         40.8      852.      104.
 9 Afghanistan  1992 16317921. Asia         41.7      649.      105.
10 Afghanistan  1997 22227415. Asia         41.8      635.      107.
# ... with 1,694 more rows
~~~
{: .output}

> ## Challenge 8
> 
> Are there any records in the gapminder dataset that don't have corresponding records
in the gapminder_sr set? 
> 
> How would you find them?
>
> > ## Solution to Challenge 8
> > There are lots of possible ways to find non-matches, but `anti_join()` is very efficient:
> >
> >~~~
> >anti_join(gapminder_sr, gapminder)
> >> {: .solution}
> >{: .challenge}
> >
> >> ## Challenge 9
> >> 
> >> Compare the output of `left`, `full` and `inner` joins for these two datasets.
> >> What are the differences? What are they due to?
> >> 
> > ## Solution to Challenge 9
> > The output from `outer_join` has the most rows, because it is keeping all the rows from both dataframes. `inner_join` is only including those rows that match.
> >> {: .solution}
> >{: .challenge}
> >
> >
> >> ## Advanced Challenge
> >>
> >> Calculate the average life expectancy in 2002 of 2 randomly selected countries
> >> for each continent. Then arrange the continent names in reverse order.
> >> **Hint:** Use the `dplyr` functions `arrange()` and `sample_n()`, they have
> >> similar syntax to other dplyr functions.
> >>
> > ## Solution to Advanced Challenge
> >~~~
> >{: .language-r}
> >
> >
> >
> >~~~
> >Error: <text>:2:1: unexpected '>'
> >1: anti_join(gapminder_sr, gapminder)
> >2: >
> >   ^
> >~~~
> >{: .error}
> >
> >~~~
> >lifeExp_2countries_bycontinents <- gapminder %>%
> >    filter(year==2002) %>%
> >    group_by(continent) %>%
> >    sample_n(2) %>%
> >    summarise(mean_lifeExp=mean(lifeExp)) %>%
> >    arrange(desc(mean_lifeExp))
> >~~~
> >{: .language-r}
> {: .solution}
{: .challenge}

## Other great resources

* [R for Data Science](http://r4ds.had.co.nz/)
* [Data Wrangling Cheat sheet](https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf)
* [Introduction to dplyr](https://cran.rstudio.com/web/packages/dplyr/vignettes/introduction.html)
* [Data wrangling with R and RStudio](https://www.rstudio.com/resources/webinars/data-wrangling-with-r-and-rstudio/)
