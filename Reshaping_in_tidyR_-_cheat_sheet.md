Reshaping in tidyr (cheat sheet)
================
Stephen Chignell
October 9, 2018

-   [Reshaping data](#reshaping-data)
    -   [Functions](#functions)
    -   [Demonstration](#demonstration)
    -   [Additional Resources](#additional-resources)

Reshaping data
--------------

One of the strengths of `dplyr` and `tidyr` is their ability to quickly change the layout of "messy" data into data structures that are conducive for exploratory analysis and graphing.

### Functions

The primary functions are below.

| Function     | What it does                     | Another way to think about it  |
|--------------|----------------------------------|--------------------------------|
| `gather()`   | transform data from wide to long | Gather columns into rows       |
| `spread()`   | transform data from long to wide | Spread rows into columns       |
| `separate()` | split 1 variable into multiple   | Separate 1 column into several |
| `unite()`    | merge multiple varialbes into 1  | Unite several columns into 1   |

-   "Wide" = More **Horizontal**
-   "Long" = More **Vertical**

**Arguments**

`gather(data, key, value, na.rm = FALSE)`

-   `data` = the data frame (can omit when piping)
-   `key` = what you want to call the new column name from headers
-   `value` = what you want to call the new column name from stacked columns
-   `na.rm` = items to include/exclude
    -   Use a "-" to speficify an exclusion
    -   You can specify single or multiple items
    -   If left blank, it will try to gather **all** columns

### Demonstration

We will demo the `spread` and `gather` functions, since they are the most commonly used. First, load the required libraries:

``` r
suppressPackageStartupMessages(library(tidyr))
suppressPackageStartupMessages(library(dplyr))
suppressPackageStartupMessages(library(knitr))
```

#### Create some sample data

Suppose you have some data on baseball teams and their runs in a number of games:

``` r
baseball <- data.frame(
   ID = LETTERS[1:10],
   Blue_Jays = sample(0:1, 5, TRUE),
   Cardinals = sample(0:1, 5, TRUE),
   Cubs = sample(0:1, 5, TRUE),
   Mariners = sample(0:1, 5, TRUE)
   )
knitr::kable(baseball)
```

| ID  |  Blue\_Jays|  Cardinals|  Cubs|  Mariners|
|:----|-----------:|----------:|-----:|---------:|
| A   |           0|          1|     1|         1|
| B   |           0|          1|     1|         1|
| C   |           0|          1|     0|         1|
| D   |           0|          0|     1|         1|
| E   |           1|          0|     1|         1|
| F   |           0|          1|     1|         1|
| G   |           0|          1|     1|         1|
| H   |           0|          1|     0|         1|
| I   |           0|          0|     1|         1|
| J   |           1|          0|     1|         1|

#### Gather the data

``` r
bb.long <- gather(baseball,Teams, Runs, -c(ID)) #exclude the "ID" field
knitr::kable(bb.long)
```

| ID  | Teams      |  Runs|
|:----|:-----------|-----:|
| A   | Blue\_Jays |     0|
| B   | Blue\_Jays |     0|
| C   | Blue\_Jays |     0|
| D   | Blue\_Jays |     0|
| E   | Blue\_Jays |     1|
| F   | Blue\_Jays |     0|
| G   | Blue\_Jays |     0|
| H   | Blue\_Jays |     0|
| I   | Blue\_Jays |     0|
| J   | Blue\_Jays |     1|
| A   | Cardinals  |     1|
| B   | Cardinals  |     1|
| C   | Cardinals  |     1|
| D   | Cardinals  |     0|
| E   | Cardinals  |     0|
| F   | Cardinals  |     1|
| G   | Cardinals  |     1|
| H   | Cardinals  |     1|
| I   | Cardinals  |     0|
| J   | Cardinals  |     0|
| A   | Cubs       |     1|
| B   | Cubs       |     1|
| C   | Cubs       |     0|
| D   | Cubs       |     1|
| E   | Cubs       |     1|
| F   | Cubs       |     1|
| G   | Cubs       |     1|
| H   | Cubs       |     0|
| I   | Cubs       |     1|
| J   | Cubs       |     1|
| A   | Mariners   |     1|
| B   | Mariners   |     1|
| C   | Mariners   |     1|
| D   | Mariners   |     1|
| E   | Mariners   |     1|
| F   | Mariners   |     1|
| G   | Mariners   |     1|
| H   | Mariners   |     1|
| I   | Mariners   |     1|
| J   | Mariners   |     1|

Notice it is now in **long** format, and "ID" has been excluded from the `gather` process.

#### Spread the data back out

``` r
bb.long %>% 
  spread(Teams, Runs) %>% #no need to specify dataset when piping
  knitr::kable()
```

| ID  |  Blue\_Jays|  Cardinals|  Cubs|  Mariners|
|:----|-----------:|----------:|-----:|---------:|
| A   |           0|          1|     1|         1|
| B   |           0|          1|     1|         1|
| C   |           0|          1|     0|         1|
| D   |           0|          0|     1|         1|
| E   |           1|          0|     1|         1|
| F   |           0|          1|     1|         1|
| G   |           0|          1|     1|         1|
| H   |           0|          1|     0|         1|
| I   |           0|          0|     1|         1|
| J   |           1|          0|     1|         1|

### Additional Resources

In order of increasing detail:

-   [tidyr in a nutshell](https://github.com/trinker/tidyr_in_a_nutshell/blob/master/README.md)
-   [RStudio Data Wrangling (Ctrl+F for "gather")](https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf)
-   [Guru99 dplyr](https://www.guru99.com/r-dplyr-tutorial.html)
-   [tidyverse documentation](https://tidyr.tidyverse.org/index.html)
