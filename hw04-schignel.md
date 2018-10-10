Homework 04: Tidy data and joins
================
Stephen Chignell
October 9, 2018

-   [Overview](#overview)
-   [Reshaping Activity \#1: Cheat Sheet](#reshaping-activity-1-cheat-sheet)
-   [Joining Activity \#1: Supplementary data](#joining-activity-1-supplementary-data)
    -   [Load libraries](#load-libraries)
    -   [Create new table](#create-new-table)
    -   [Left Join](#left-join)
    -   [Right Join](#right-join)
    -   [Inner Join](#inner-join)
    -   [Anti Join](#anti-join)
    -   [Full Join](#full-join)

Overview
--------

**Goal:** Solidify data wrangling skills by working on some realistic problems in the grey area between data aggregation and data reshaping.

**Assignment:** This is a “choose your own adventure”-style assignment, where you are expected to do the following two things:

-   Pick one of the data reshaping prompts and do it.
-   Pick one of the join prompts and do it.

It is fine to work with a new dataset and/or create variations on these problem themes.

Reshaping Activity \#1: Cheat Sheet
-----------------------------------

I have developed a separate reshaping [Cheat Sheet](https://github.com/STAT545-UBC-students/hw04-schignel/blob/master/Reshaping_in_tidyR_-_cheat_sheet.md), which is stored in my Homework 4 repository.

Joining Activity \#1: Supplementary data
----------------------------------------

**Instructions:** Create a second data frame, complementary to Gapminder. Join this with (part of) Gapminder using a dplyr join function and make some observations about the process and result.

#### Load libraries

``` r
suppressPackageStartupMessages(library(tidyr))
suppressPackageStartupMessages(library(dplyr))
suppressPackageStartupMessages(library(knitr))
suppressPackageStartupMessages(library(gapminder))
```

#### Create new table

Next, we will create a new data frame with supplementary information to join to`gapminder`.

``` r
# Create a list of country names
country <- c("Argentina", "Ethiopia", "Senegal", "Indonesia", "China", "Canada", "Germany", "Australia", "South Sudan") 

# List area (km2) for each country
area_km2 <- c(2780400, 1127127, 196190, 1919440, 9706961, 9984670, 357021, 7692024, 619745)

# List capital city for each country
capital <- c("Buenos Aires", "Addis Ababa", "Dakar", "Jakarta", "Beijing", "Ottowa", "Berlin", "Canberra", "Juba")

#Save vectors to new data frame
supp <- data.frame(country, area_km2, capital)

#View supplementary data frame as a nice table
knitr::kable(supp, caption = "Supplementary country data")
```

| country     |  area\_km2| capital      |
|:------------|----------:|:-------------|
| Argentina   |    2780400| Buenos Aires |
| Ethiopia    |    1127127| Addis Ababa  |
| Senegal     |     196190| Dakar        |
| Indonesia   |    1919440| Jakarta      |
| China       |    9706961| Beijing      |
| Canada      |    9984670| Ottowa       |
| Germany     |     357021| Berlin       |
| Australia   |    7692024| Canberra     |
| South Sudan |     619745| Juba         |

**Supplementary data sources**

-   [Country areas](https://simple.wikipedia.org/wiki/List_of_countries_by_area?oldformat=true)
-   [Country capitals](https://www.countries-ofthe-world.com/capitals-of-the-world.html)

We are now ready to practice different types of joins between the supplementatry data frame and the gapminder dataset.

#### Left Join

`left_join` is the most common way to merge to data sets:

``` r
#Left join and pipe to str() to inspect without generating massive table
left_join(gapminder, supp, by = "country") %>% 
  str() 
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    1704 obs. of  8 variables:
    ##  $ country  : chr  "Afghanistan" "Afghanistan" "Afghanistan" "Afghanistan" ...
    ##  $ continent: Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
    ##  $ pop      : int  8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
    ##  $ gdpPercap: num  779 821 853 836 740 ...
    ##  $ area_km2 : num  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ capital  : Factor w/ 9 levels "Addis Ababa",..: NA NA NA NA NA NA NA NA NA NA ...

Because we specified `gapminder` as the lefthand column, `left_join` kept all of the `gapminder` records while successfully joining the `area_km2` and `capital` variables to it. Notice that the funtion has inserted "NA" values for countries not listed in the supplementary table.

#### Right Join

`right_join` is the same as `left_join`, except that it returns all records from the righthand table (while retaining all columns from both tables).

``` r
#Right join and pipe to str()
right_join(gapminder, supp, by = "country") %>% 
  str()
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    97 obs. of  8 variables:
    ##  $ country  : chr  "Argentina" "Argentina" "Argentina" "Argentina" ...
    ##  $ continent: Factor w/ 5 levels "Africa","Americas",..: 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp  : num  62.5 64.4 65.1 65.6 67.1 ...
    ##  $ pop      : int  17876956 19610538 21283783 22934225 24779799 26983828 29341374 31620918 33958947 36203463 ...
    ##  $ gdpPercap: num  5911 6857 7133 8053 9443 ...
    ##  $ area_km2 : num  2780400 2780400 2780400 2780400 2780400 ...
    ##  $ capital  : Factor w/ 9 levels "Addis Ababa",..: 4 4 4 4 4 4 4 4 4 4 ...

Here we see that the `gapminder` dataset has been reduced from 1704 to 97 observations, and that the two additional variables from the supplementary table have been joined.

#### Inner Join

What if we wanted an output containing only the countries listed in both datasets?

For this we use the `inner_join` function:

``` r
#Inner join and nice table to preview
knitr::kable(head(inner <- inner_join(gapminder, supp, by = "country")))
```

| country   | continent |  year|  lifeExp|       pop|  gdpPercap|  area\_km2| capital      |
|:----------|:----------|-----:|--------:|---------:|----------:|----------:|:-------------|
| Argentina | Americas  |  1952|   62.485|  17876956|   5911.315|    2780400| Buenos Aires |
| Argentina | Americas  |  1957|   64.399|  19610538|   6856.856|    2780400| Buenos Aires |
| Argentina | Americas  |  1962|   65.142|  21283783|   7133.166|    2780400| Buenos Aires |
| Argentina | Americas  |  1967|   65.634|  22934225|   8052.953|    2780400| Buenos Aires |
| Argentina | Americas  |  1972|   67.065|  24779799|   9443.039|    2780400| Buenos Aires |
| Argentina | Americas  |  1977|   68.481|  26983828|  10079.027|    2780400| Buenos Aires |

We can see that the variables `area_km2` and `capital` are joined, but we can't see from this table whether or not it removed the records that didn't match. Let's check with the `str()` function.

``` r
#Look at the structure
str(inner)
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    96 obs. of  8 variables:
    ##  $ country  : chr  "Argentina" "Argentina" "Argentina" "Argentina" ...
    ##  $ continent: Factor w/ 5 levels "Africa","Americas",..: 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp  : num  62.5 64.4 65.1 65.6 67.1 ...
    ##  $ pop      : int  17876956 19610538 21283783 22934225 24779799 26983828 29341374 31620918 33958947 36203463 ...
    ##  $ gdpPercap: num  5911 6857 7133 8053 9443 ...
    ##  $ area_km2 : num  2780400 2780400 2780400 2780400 2780400 ...
    ##  $ capital  : Factor w/ 9 levels "Addis Ababa",..: 4 4 4 4 4 4 4 4 4 4 ...

Just as we would expect `gapminder` has been reduced from 1704 to 96 observations, and the two additional variables from the supplementary table have been joined.

**Question** Why does the `inner_join` output have 96 observations while the `right_join` output had 97?

#### Anti Join

We can find the source of this discrepancy using an `anti_join`, which returns all rows from the first table where there are not matching values in the second, filtering the output to include just columns from the first table.

``` r
# Supplementary table is first listed to identify discrepancy
knitr::kable(anti_join(supp, gapminder, by = "country"))
```

| country     |  area\_km2| capital |
|:------------|----------:|:--------|
| South Sudan |     619745| Juba    |

Interpretating this result, we can recall that South Sudan became a country in 2011, and is therefore not included in the `gapminder` data set, which includes data up to 2007.

#### Full Join

To keep all data in both tables, we can use the `full_join` function

``` r
str((full_join(gapminder, supp, by = "country")))
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    1705 obs. of  8 variables:
    ##  $ country  : chr  "Afghanistan" "Afghanistan" "Afghanistan" "Afghanistan" ...
    ##  $ continent: Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
    ##  $ pop      : int  8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
    ##  $ gdpPercap: num  779 821 853 836 740 ...
    ##  $ area_km2 : num  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ capital  : Factor w/ 9 levels "Addis Ababa",..: NA NA NA NA NA NA NA NA NA NA ...

Here we see that the output has 1705 observations of 8 variables, indicating that operation has kept all data from both tables, and replaced missing data cells with "NA".
