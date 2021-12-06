---
title: Working in the tidyverse
author: twg
date: '2021-12-05'
slug: working-in-the-tidyverse
categories:
  - tidyverse
  - dplyr
  - tidy
tags:
  - tidyverse
  - dplyr
  - tidy
subtitle: ''
summary: 'Tips and tricks for working in the tidyverse'
authors: []
lastmod: '2021-12-05T16:41:07-08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---





## Using setNames to change column names


```r
names <- c("name1", "name2", "name3", "name4")

df <- tibble(
  a = sample(letters, 4),
  b = sample(1:100, 4),
  c = sample(1:100, 4),
  d = sample(letters, 4)
)

df %>% setNames(names)
```

```
## # A tibble: 4 × 4
##   name1 name2 name3 name4
##   <chr> <int> <int> <chr>
## 1 c         9    78 a    
## 2 p        74     4 w    
## 3 n        87     8 u    
## 4 l         2    61 o
```

## Using rename_at to rename specific columns 

Occasionally, you'll only want to rename certain columns and the `rename_at` function offers this capability, in much the same way as `mutate_at` or `summarise_at`. 


```r
df <- tibble(
  a = sample(letters, 4),
  b = sample(1:100, 4),
  c = sample(1:100, 4),
  d = sample(letters, 4)
)

df %>% 
  rename_at(vars(b,d), ~ paste0("pastedName_", .))
```

```
## # A tibble: 4 × 4
##   a     pastedName_b     c pastedName_d
##   <chr>        <int> <int> <chr>       
## 1 o               87    64 v           
## 2 z               45    76 l           
## 3 j               29    88 u           
## 4 t               20    41 i
```

## Mutate and Summarise multiple columns 

This is well documented [here](https://dplyr.tidyverse.org/reference/across.html), but a few examples are below. When using `across()` with we need to wrap the variables in quotes if we're mentioning multiple. And if we're using multiple functions they need to be put into a `list()`. To keep tabs on the results, name the function output. 


```r
mtcars %>%
  group_by(cyl) %>%
  summarise(across(c("disp", "drat", "mpg"), list(mean = mean, sd = sd)))
```

```
## # A tibble: 3 × 7
##     cyl disp_mean disp_sd drat_mean drat_sd mpg_mean mpg_sd
##   <dbl>     <dbl>   <dbl>     <dbl>   <dbl>    <dbl>  <dbl>
## 1     4      105.    26.9      4.07   0.365     26.7   4.51
## 2     6      183.    41.6      3.59   0.476     19.7   1.45
## 3     8      353.    67.8      3.23   0.372     15.1   2.56
```

The [helper functions](https://dplyr.tidyverse.org/reference/select_helpers.html) for `select()` are also useful for selecting variables. Though note that when using helpers such as `contains()` that you can only include one string. For instance, this will work.


```r
mtcars %>% 
  summarise(across(contains("ar"), mean))
```

```
##     gear   carb
## 1 3.6875 2.8125
```

But this won't


```r
mtcars %>%
  summarise(across(contains("ar|mp"), mean))
```

```
## data frame with 0 columns and 1 row
```

If you want to match across multiple strings, the `matches()` function will do the trick. 


```r
mtcars %>%
  summarise(across(matches("ar|mp"), mean))
```

```
##        mpg   gear   carb
## 1 20.09062 3.6875 2.8125
```

## Multiple left_joins using dplyr

It's always possible to use multiple `left_join` functions, but the easiest way to do merge multiple data sets together may be to put everything into a list and then use the `Reduce` function. I had used `map` to work over a lot of data, so everything was in a list. I then took the data I wanted to use as my base and concatenated it to the list. 


```r
tmp <- c(list(df), original_list)
```

Then, using dplyr commands was able to join all of the data. In this case, my original list had 30 separate dataframes.


```r
mass_df <- tmp %>% Reduce(function(df1, df2), left_join(df1, df2), .)
```

The `left_join` command can be further defined to specify what we're joining by, or to select only specific columns that will be joined. 


```r
# using "matches" to pull out specific variables
mass_df <- tmp %>% 
  Reduce(function(df1, df2), left_join(df1, select(df2, matches("avar|bvar"))), .)
```


```r
# specifying what the join is by
mass_df <- tmp %>% Reduce(function(df1, df2), left_join(df1, df2, by = "index"), .)
```

## Calculating quantiles in tidy fashion 


```r
pacman::p_load(tidyverse)
# set up quantiles we want 
p <- c(.2, .4, .6, .8)
# create list of functions; one for each quantile; and give names to each
p_names <- map_chr(p, ~paste0('p_',.x*100))

p_funs <- map(p, ~partial(quantile, probs = .x, na.rm = TRUE)) %>%
  set_names(nm = p_names)

mtcars %>% group_by(cyl) %>% 
  summarise(across(mpg, tibble::lst(!!!p_funs))) %>% 
  janitor::clean_names()
```

```
## # A tibble: 3 × 5
##     cyl mpg_p_20 mpg_p_40 mpg_p_60 mpg_p_80
##   <dbl>    <dbl>    <dbl>    <dbl>    <dbl>
## 1     4     22.8     24.4     27.3     30.4
## 2     6     18.3     19.4     20.5     21  
## 3     8     13.9     15.0     15.4     16.8
```

## Selection of multiple variables

When using either `select()` or wanting to `mutate(across())` there are lots of helpers -- `starts_with()`, `ends_with()`, `contains()`, and `matches()` and probably some that I'm missing. 

`contains()` only works on a single, specific request, whereas `matches()` allows for an OR. 


```r
tmp <- tibble(g1_letters = sample(letters, 5), 
              g1_num = sample(1:600000, 5),
              g2_letters = sample(letters, 5),
              g2_num = sample(1:60000, 5),
              h1_letters = sample(letters, 5),
              h1_num = sample(1:60000, 5))

# or statement
tmp <- tmp %>% select(matches("g1|h1"))
tmp
```

```
## # A tibble: 5 × 4
##   g1_letters g1_num h1_letters h1_num
##   <chr>       <int> <chr>       <int>
## 1 p          362388 q            4476
## 2 g           24763 g           13974
## 3 l          188127 y           13895
## 4 q          339255 k           51834
## 5 r           42666 s           39125
```

But we can also use the `intersect()` function to create an AND statement


```r
tmp <- tibble(g1_letters = sample(letters, 5), 
              g1_num = sample(1:600000, 5),
              g1_weighted = g1_num*.4,
              g2_letters = sample(letters, 5),
              g2_num = sample(1:60000, 5),
              g2_weighted = g2_num*.4,
              h1_letters = sample(letters, 5),
              h1_num = sample(1:60000, 5),
              h1_weighted = h1_num*.4)

tmp %>% 
  summarise(across(matches("num|weighted"), sum)) %>%
  mutate(across(intersect(starts_with("g1"), contains("weighted")), 
            ~ paste0(., "---->")))
```

```
## # A tibble: 1 × 6
##    g1_num g1_weighted   g2_num g2_weighted h1_num h1_weighted
##     <int> <chr>          <int>       <dbl>  <int>       <dbl>
## 1 1523891 609556.4----> 154702      61881. 173043      69217.
```

## Piping into a t.test


```r
tibble(a = c(rnorm(100, mean = 50, sd = 5),rnorm(100, mean = 60, sd = 5)),
       group = c(rep("green", 100), rep("blue", 100))) %>%
  t.test(a ~ group, data = ., var.equal = TRUE)
```

```
## 
## 	Two Sample t-test
## 
## data:  a by group
## t = 15.88, df = 198, p-value < 2.2e-16
## alternative hypothesis: true difference in means between group blue and group green is not equal to 0
## 95 percent confidence interval:
##   9.368624 12.025324
## sample estimates:
##  mean in group blue mean in group green 
##            60.35729            49.66031
```

## Arranging within a group


```r
library(tidyverse)
ToothGrowth %>%
    group_by(supp) %>%
    arrange(len, .by_group = TRUE)
```

```
## # A tibble: 60 × 3
## # Groups:   supp [2]
##      len supp   dose
##    <dbl> <fct> <dbl>
##  1   8.2 OJ      0.5
##  2   9.4 OJ      0.5
##  3   9.7 OJ      0.5
##  4   9.7 OJ      0.5
##  5  10   OJ      0.5
##  6  14.5 OJ      0.5
##  7  14.5 OJ      1  
##  8  15.2 OJ      0.5
##  9  16.5 OJ      0.5
## 10  17.6 OJ      0.5
## # … with 50 more rows
```

## Using `cross` and `map` to paste

The `cross()` function is similar to `expand.grid`. Here, we create a list and use `map` to paste it together where each in a separate list. 


```r
data <- list(qq = "Q",
             q = 1:4,
             hyphen = "-",
             yr = 13:19)

data %>%
  cross() %>%
  map(lift(paste0)) %>% 
  head()
```

```
## [[1]]
## [1] "Q1-13"
## 
## [[2]]
## [1] "Q2-13"
## 
## [[3]]
## [1] "Q3-13"
## 
## [[4]]
## [1] "Q4-13"
## 
## [[5]]
## [1] "Q1-14"
## 
## [[6]]
## [1] "Q2-14"
```

But we can use `setNames` and then reduce put it into a data.frame. 


```r
data <- list(qq = "Q",
             q = 1:4,
             hyphen = "-",
             yr = 13:19)

data %>%
  cross() %>%
  map(lift(paste0)) %>% 
  map(setNames, c("QTR")) %>% 
  reduce(bind_rows) %>%
  head()
```

```
## # A tibble: 6 × 1
##   QTR  
##   <chr>
## 1 Q1-13
## 2 Q2-13
## 3 Q3-13
## 4 Q4-13
## 5 Q1-14
## 6 Q2-14
```

## Group indices within nested groups 

Assume we have a data set with a nested group structures like so. 


```r
tibble(letter = rep(letters[1:2], each = 6),
       state = c(rep(c(state.abb[c(1,4,5)]), each = 2),
                 rep(c(state.abb[c(25,38,43)]), each = 2)))
```

```
## # A tibble: 12 × 2
##    letter state
##    <chr>  <chr>
##  1 a      AL   
##  2 a      AL   
##  3 a      AR   
##  4 a      AR   
##  5 a      CA   
##  6 a      CA   
##  7 b      MO   
##  8 b      MO   
##  9 b      PA   
## 10 b      PA   
## 11 b      TX   
## 12 b      TX
```

Our goal is to produce a repeating id for each state within each letter group, so that AL, AR, and CA would be 1, 2, 3 and MO, PA, and TX would also be 1, 2, 3. 

But using the `group_indices()` function doesn't help us here (note I'm suppressing warnings because I have no idea how to use `group_by()` first with this function).

```r
tibble(letter = rep(letters[1:2], each = 6),
       state = c(rep(c(state.abb[c(1,4,5)]), each = 2),
                 rep(c(state.abb[c(25,38,43)]), each = 2))) %>%
  mutate(id1 = suppressWarnings(group_indices(., letter)),
         id2 = suppressWarnings(group_indices(., state)))
```

```
## # A tibble: 12 × 4
##    letter state   id1   id2
##    <chr>  <chr> <int> <int>
##  1 a      AL        1     1
##  2 a      AL        1     1
##  3 a      AR        1     2
##  4 a      AR        1     2
##  5 a      CA        1     3
##  6 a      CA        1     3
##  7 b      MO        2     4
##  8 b      MO        2     4
##  9 b      PA        2     5
## 10 b      PA        2     5
## 11 b      TX        2     6
## 12 b      TX        2     6
```
But we can get to what we want by using `cumsum` and `!duplicated`...

```r
tibble(letter = rep(letters[1:2], each = 6),
       state = c(rep(c(state.abb[c(1,4,5)]), each = 2),
                 rep(c(state.abb[c(25,38,43)]), each = 2))) %>%
  group_by(letter) %>%
  mutate(id = cumsum(!duplicated(state)))
```

```
## # A tibble: 12 × 3
## # Groups:   letter [2]
##    letter state    id
##    <chr>  <chr> <int>
##  1 a      AL        1
##  2 a      AL        1
##  3 a      AR        2
##  4 a      AR        2
##  5 a      CA        3
##  6 a      CA        3
##  7 b      MO        1
##  8 b      MO        1
##  9 b      PA        2
## 10 b      PA        2
## 11 b      TX        3
## 12 b      TX        3
```