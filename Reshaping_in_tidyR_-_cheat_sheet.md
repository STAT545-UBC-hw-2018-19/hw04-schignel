Reshaping in tidyR (cheat sheet)
================
Stephen Chignell
October 9, 2018

-   [Reshaping data](#reshaping-data)

Reshaping data
--------------

One of the strengths of dplyr and tidyr is their ability to quickly change the layout of "messy" data into data structures that are more conducive for exploratory analysis and graphing.

The most important functions are:

| Function     | What it does                      | Another way to think about it    |
|--------------|-----------------------------------|----------------------------------|
| `gather()`   | transform data from wide to long  | Gather columns into rows         |
| `spread()`   | transform data from long to wide  | Spread rows into columns         |
| `separate()` | split 1 variable into multiple    | Separate one column into several |
| `unite()`    | merge multiple varialbes into one | Unite several columns into one   |
