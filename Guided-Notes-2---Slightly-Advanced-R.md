---
title: "Guided Notes 2: Introduction to slightly advanced R tasks"
output:
  html_document:
    keep_md: TRUE #this keeps the intermediate markdown file when producing an HTML file
  pdf_document:
    toc: yes #see comments above for pdf information
    toc_depth: '3'
  html_notebook:
    fig_caption: yes #adds the ability to use figure captions, see this for more info: https://stackoverflow.com/questions/31926623/figures-captions-and-labels-in-knitr
    toc: yes #adds a table of contents
    toc_depth: 3 #specifies how many header levels count for the table of contents
    toc_float: yes #does the TOC float down the page
---

#### *Your name & ID*

------------------------------------------------------------------------

## 0. Best Practices - Load Libraries first!

One of the main reasons we're using R and R Studio is to capture the process of "reproducibility". That is, your code should be:

1.  easy to read.

2.  easy to follow and

3.  contain all the information a naive user might need to successfully replicate your analysis.

One very polite thing to do is inform the user (and that user might be *you* a few months from now!) what packages you might need, and any information needed to install them.

For this exercise we will be using the `tidyverse`, perhaps unsurprisingly.


```r
#While I said we wouldn't use hashtags that often, that was a lie
#It's still useful inside code chunks to provide code that the user MIGHT need to run, or more specifically detailed instructions
#in this case we will say...
#if the tidyverse isn't installed, please run the line below to install it by removing the hashtag, but remember to put the hashtag back!
#install.packages("tidyverse")
library("tidyverse")
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.4
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
## ✔ purrr     1.0.2     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

------------------------------------------------------------------------

## 1. Data Import two ways

First, go to moodle and download the dataset titled "school". Make sure to save it in the same folder where you saved this script you're currently working on.

Data import from your computer directly is, surprisingly, one of the more difficult problems in R. I expect this exercise to fail spectacularly for at least one of you.

We are first going to import the data into an object named, school1.


```r
school1 <- read.csv("schools.csv")
```

Hooray, "real" data imported into R! Note that if you know your way around file systems, you can provide the absolute path to the file you'd like inside the `read.csv()` function.

However....`read.csv()` is actually a function in *base R*, not in the `tidyverse`. (You can see this by typing it and letting the dynamic editor try and "help".)

We're going to use `tidyverse`'s version of the command to import a \*.csv file, which is, annoyingly, named `read_csv()`.

Try it now, saving the data into a new object named `school2` using `read_csv()`.


```r
school2 <- read_csv("schools.csv")
```

```
## Rows: 1000 Columns: 8
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (5): gender, race_ethnicity, parental_level_of_education, lunch, test_pr...
## dbl (3): math_score, reading_score, writing_score
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

In the chunk below, inspect the two different objects. While there are many similarities, there a few subtle differences. For one, `school2` and `school1` have different *classes*, which means that (depending on your function) some functions might not accept one over the other.

Use the `class()` function below to inspect both objects.


```r
class(school1)
```

```
## [1] "data.frame"
```

```r
class(school2)
```

```
## [1] "spec_tbl_df" "tbl_df"      "tbl"         "data.frame"
```

For the majority of uses, tibbles and dataframes are nearly equivalent, although tibbles have a few specific advantages for the data scientist, which we will talk about in a bit.

For the rest of today's exercises, we will be using `school1`, but end with a quick detour into some of the features unique to `school2`.

------------------------------------------------------------------------

## 2. Using our basic tools to poke at some data.

Most of the time you don't engage with the entire dataframe at once, you pull out individual columns to perform calculations, clean, and visualize.

We're going to be a little bit repetitive for the sake of practice.

In the code chunk below, create two new objects, one containing all of the writing scores, and one containing all of the math scores. Save them as `ws` and `ms`, respectively.


```r
ws<- school1[,8]
ws1 <- school1$writing_score

sum(ws == ws1)
```

```
## [1] 1000
```

```r
ms <- school1$math_score

sum(ms == ws)
```

```
## [1] 35
```

Now that we have pulled out the columns we want, we can perform some basic calculations on them.

Let's get some summary statistics! Apply the functions `mean()`, `min()`, and `max()` to both objects.


```r
mean(ms)
```

```
## [1] 66.089
```

```r
min(school1$math_score)
```

```
## [1] 0
```

```r
max(school1[,6])
```

```
## [1] 100
```

```r
mean(ws)
```

```
## [1] 68.054
```

```r
min(ws)
```

```
## [1] 10
```

```r
max(ws)
```

```
## [1] 100
```

There's a faster way to get quick information about a data set. Enter the `summary()` function!


```r
summary(school1)
```

```
##     gender          race_ethnicity     parental_level_of_education
##  Length:1000        Length:1000        Length:1000                
##  Class :character   Class :character   Class :character           
##  Mode  :character   Mode  :character   Mode  :character           
##                                                                   
##                                                                   
##                                                                   
##     lunch           test_preparation_course   math_score     reading_score   
##  Length:1000        Length:1000             Min.   :  0.00   Min.   : 17.00  
##  Class :character   Class :character        1st Qu.: 57.00   1st Qu.: 59.00  
##  Mode  :character   Mode  :character        Median : 66.00   Median : 70.00  
##                                             Mean   : 66.09   Mean   : 69.17  
##                                             3rd Qu.: 77.00   3rd Qu.: 79.00  
##                                             Max.   :100.00   Max.   :100.00  
##  writing_score   
##  Min.   : 10.00  
##  1st Qu.: 57.75  
##  Median : 69.00  
##  Mean   : 68.05  
##  3rd Qu.: 79.00  
##  Max.   :100.00
```

```r
summary(school1$reading_score)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   17.00   59.00   70.00   69.17   79.00  100.00
```

### 2.1 Mini challenge

Turn the math score into a percentage and store it as a new object named `ms_perc`.


```r
ms_perc <- school1$math_score/100
ms_perc1 <- ms/100
school1$ms_perc <- ms/100
```

### 2.2 Poking at character columns

Text and factor columns can't be fed into functions such as `mean()` for reasons I hope are obvious. However, we can still work with them.

Create a new object called `parent_ed` that has the level of education of the parents in this data set.


```r
parent_ed <- school1$parental_level_of_education
```

One thing that's really helpful to do with text- and factor-level columns is to identify unique "strings" of words, letters, and symbols. It's one of the fastest ways to identify errors in data you're working with. Remember, R is *incredibly* case-sensitive, so "college" is different than "College", which has important implications for analysis.

Use the `unique` function to investigate the `parent_ed` object you just made.


```r
length(unique(parent_ed))
```

```
## [1] 6
```

### 2.3 Mini challenge 2

In the space below, let's calculate the mean test score (i.e. the average for all three exams) for each student. There are many ways to do this! Before we start, let's take a minute and "pseudo-code" the problem!

*notes on pseudo-coding go here, if needed*

In the space below, add your answer to the problem


```r
rs <- school1$reading_score
avg_score <- (ms + ws + rs) / 3
```

Finally, let's add this column back to our original dataframe, `school1`.


```r
school1$avg_score <- avg_score
```

### 2.4 Other solutions to the Mini challenge

In the space below, let's add other people's solutions, if there are any differences.


```r
school1$avg_score2 <- (school1$math_score + school1$reading_score + school1$writing_score) /3
```

## 3. Finally touching the tidyverse

We're going to briefly look at the subtle differences in the tibble versus dataframe class, and then I'm going to show you how to perform the analysis we did a bit ago in Mini Challenge 2 using "tidyverse logic".

### 3.1 Tibbles versus dataframes.

In the CONSOLE, type the name of each object (`school1` and `school2`). What do you notice? What are the advantages of a tibble over a dataframe here?

What you should notice is that tibbles only return a truncated view when printed to the screen, something that doesn't matter when you're working with small datasets, but with even moderately sized datasets becomes incredibly useful. Another useful feature of tibbles is that they can accept a larger number of column names than traditional data frames, which is useful when you're trying to clean and sanitize data that you didn't personally generate.

### 3.2 Minor flex with tidyverse

One of the advantages of the tidyverse is many of the commands allow you to dispense with having to use `[]` notation to get the columns you want to work with, instead being able to just type the name of the column.

See below for an example of how to calculate the mean of multiple columns with tidyverse


```r
school2 %>%
  rowwise() %>% mutate(avg_score = mean(c(math_score, reading_score, writing_score)))
```

```
## # A tibble: 1,000 × 9
## # Rowwise: 
##    gender race_ethnicity parental_level_of_educat…¹ lunch test_preparation_cou…²
##    <chr>  <chr>          <chr>                      <chr> <chr>                 
##  1 female group B        bachelor's degree          stan… none                  
##  2 female group C        some college               stan… completed             
##  3 female group B        master's degree            stan… none                  
##  4 male   group A        associate's degree         free… none                  
##  5 male   group C        some college               stan… none                  
##  6 female group B        associate's degree         stan… none                  
##  7 female group B        some college               stan… completed             
##  8 male   group B        some college               free… none                  
##  9 male   group D        high school                free… completed             
## 10 female group B        high school                free… none                  
## # ℹ 990 more rows
## # ℹ abbreviated names: ¹​parental_level_of_education, ²​test_preparation_course
## # ℹ 4 more variables: math_score <dbl>, reading_score <dbl>,
## #   writing_score <dbl>, avg_score <dbl>
```

This, on the surface, *looks* like more work, but as you become more familiar with the tidyverse, it ends up being quite a bit less work, much more efficient, and as a bonus, doesn't create a bunch of extra objects to calculate, all of which become more and more important as your datasets get larger and larger.

That's it for this set of notes! Make sure to clear out your environment when you're done!

*spaces for other notes here*
