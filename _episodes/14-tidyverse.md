---
title: Intro to the tidyverse
teaching: 40
exercises: 15
questions:
- "How can I use a consistent underlying data structure?"
objectives:
- "To recognise tidy vs messy data"
- "To know where to find more information about the tidyverse"
keypoints:
- "For data to be tidy, each variable must be in its own column"
- "For data to be tidy, each case must be in its own row"
- "For data to be tidy, each value must be in its own cell"
source: Rmd
---



> Happy families are all alike; every unhappy family is unhappy in its own way
>
> *Leo Tolstoy*

> Tidy data are all alike; every untidy data is untidy in its own way
>
>*Hadley Wickham*

Data can come in many different shapes and forms, and often people invent whatever makes sense to them. This often means that a great deal of time is spent modifying data to be structured in a format that R can use.

Within R, different packages can have different expectations about data structures, which can make it difficult to move between functions in different packages.

The [tidyverse](https://www.tidyverse.org/) is a subset of R packages that conform to a particular philosophy about data structure.

The concept of tidy data can be distilled into three principles. A data set can be considered 'tidy' if:

1. Each variable is in its own column
2. Each case is in its own row
3. Each value is in its own cell



> ## Challenge 1
>
> In the following table, what makes it untidy?
><table class="table" style="width: auto !important; ">
> <thead>
>  <tr>
>   <th style="text-align:right;"> id </th>
>   <th style="text-align:right;"> rep1 </th>
>   <th style="text-align:right;"> rep2 </th>
>  </tr>
> </thead>
><tbody>
>  <tr>
>   <td style="text-align:right;"> 1 </td>
>   <td style="text-align:right;"> 1.44 </td>
>   <td style="text-align:right;"> 2.07 </td>
>  </tr>
>  <tr>
>   <td style="text-align:right;"> 2 </td>
>   <td style="text-align:right;"> 1.77 </td>
>   <td style="text-align:right;"> 2.13 </td>
>  </tr>
>  <tr>
>   <td style="text-align:right;"> 3 </td>
>   <td style="text-align:right;"> 3.56 </td>
>   <td style="text-align:right;"> 3.72 </td>
>  </tr>
></tbody>
></table>
{: .challenge}

> ## Challenge 2
>
> What it would look like if it was tidy?
> > ## Solution to Challenge 2
> ><table class="table" style="width: auto !important; ">
> > <thead>
> >  <tr>
> >   <th style="text-align:right;"> id </th>
> >   <th style="text-align:left;"> rep </th>
> >   <th style="text-align:right;"> value </th>
> >  </tr>
> > </thead>
> ><tbody>
> >  <tr>
> >   <td style="text-align:right;"> 1 </td>
> >   <td style="text-align:left;"> 1 </td>
> >   <td style="text-align:right;"> 1.44 </td>
> >  </tr>
> >  <tr>
> >   <td style="text-align:right;"> 2 </td>
> >   <td style="text-align:left;"> 1 </td>
> >   <td style="text-align:right;"> 1.77 </td>
> >  </tr>
> >  <tr>
> >   <td style="text-align:right;"> 3 </td>
> >   <td style="text-align:left;"> 1 </td>
> >   <td style="text-align:right;"> 3.56 </td>
> >  </tr>
> >  <tr>
> >   <td style="text-align:right;"> 1 </td>
> >   <td style="text-align:left;"> 2 </td>
> >   <td style="text-align:right;"> 2.07 </td>
> >  </tr>
> >  <tr>
> >   <td style="text-align:right;"> 2 </td>
> >   <td style="text-align:left;"> 2 </td>
> >   <td style="text-align:right;"> 2.13 </td>
> >  </tr>
> >  <tr>
> >   <td style="text-align:right;"> 3 </td>
> >   <td style="text-align:left;"> 2 </td>
> >   <td style="text-align:right;"> 3.72 </td>
> >  </tr>
> ></tbody>
> ></table>
> {: .solution}
{: .challenge}

In the above the same variable (a measurement value) was stored in two different columns. In this case making the data tidy required converting those two columns into one, which made the dataset have twice as many rows. This is usually called going from "wide" to "long" format, which is often done in the simplest cases of tidying data.


> ## Challenge 3
>
> Open the file `plates.xlsx` (download [here]({{ page.root }}/data/gapminder_sex_ratios.csv)). This is a very common format to store data
> from 96-well plates. What would this look like if it was tidy? Discuss the steps you would need to go through to convert it to a tidy format.
{: .challenge}

As well as consistency of the underlying data structure, packages in the tidyverse are also all compatible with the `%>%` operator.

There is a tidyverse package, which doesn't have any functionality, except to import core packages of the tidyverse. 

~~~
library(tidyverse)
~~~
{: .language-r}



~~~
Warning: replacing previous import by 'tidyr::%>%' when loading 'broom'
~~~
{: .error}



~~~
Warning: replacing previous import by 'tidyr::gather' when loading 'broom'
~~~
{: .error}



~~~
Warning: replacing previous import by 'tidyr::spread' when loading 'broom'
~~~
{: .error}



~~~
── Attaching packages ────────────────────────────────── tidyverse 1.2.1 ──
~~~
{: .output}



~~~
✔ tibble  1.4.2     ✔ purrr   0.2.4
✔ tibble  1.4.2     ✔ forcats 0.3.0
~~~
{: .output}



~~~
── Conflicts ───────────────────────────────────── tidyverse_conflicts() ──
✖ dplyr::filter() masks stats::filter()
✖ dplyr::lag()    masks stats::lag()
~~~
{: .output}

is the equivalent of 


~~~
library(ggplot2)
library(dplyr)
library(tidyr)
library(readr)
library(purrr)
library(tibble)
library(stringr)
library(forcats)
~~~
{: .language-r}


## Other great resources

* [The original tidy data paper](https://www.jstatsoft.org/article/view/v059i10/v59i10.pdf)
