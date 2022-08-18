---
title: Working in the tidyverse
author: twg
date: '2022-08-18'
categories:
  - dplyr
  - tidy
  - tidyverse
tags:
  - dplyr
  - tidy
  - tidyverse
slug: working-in-the-tidyverse
summary: Tips and tricks for working in the tidyverse
lastmod: '2021-12-05T16:41:07-08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
---





## Writing files to Google Sheets

This requires authorization, hopefully the Oauth token can be auto-refreshed. 


```r
# write this to Google Sheets 
pacman::p_load(googlesheets4, googledrive)
# if want to write multiple sheets
my_df <- list(df_name1 = df1, df_name2 = df2)
ss4 <- googlesheets4::gs4_create(
  "SHEET NAME",
  sheets = my_df
)
```

## Converting named list to dataframe keeping column with the names

This is using the `data.table` package


```r
# named list
ll <- mtcars %>%
    group_by(cyl) %>%
    group_map(~ head(.x, 1L)) %>% 
  set_names(c("Mazda", "Honda", "Suzuki"))
ll
```

```
## $Mazda
## # A tibble: 1 × 10
##     mpg  disp    hp  drat    wt  qsec    vs    am  gear  carb
##   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
## 1  22.8   108    93  3.85  2.32  18.6     1     1     4     1
## 
## $Honda
## # A tibble: 1 × 10
##     mpg  disp    hp  drat    wt  qsec    vs    am  gear  carb
##   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
## 1    21   160   110   3.9  2.62  16.5     0     1     4     4
## 
## $Suzuki
## # A tibble: 1 × 10
##     mpg  disp    hp  drat    wt  qsec    vs    am  gear  carb
##   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
## 1  18.7   360   175  3.15  3.44  17.0     0     0     3     2
```

```r
data.table::rbindlist(ll, idcol = 'make') %>% tibble()
```

```
## # A tibble: 3 × 11
##   make     mpg  disp    hp  drat    wt  qsec    vs    am  gear  carb
##   <chr>  <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
## 1 Mazda   22.8   108    93  3.85  2.32  18.6     1     1     4     1
## 2 Honda   21     160   110  3.9   2.62  16.5     0     1     4     4
## 3 Suzuki  18.7   360   175  3.15  3.44  17.0     0     0     3     2
```

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
## 1 o        19    14 b    
## 2 z        67    68 j    
## 3 v        89    17 o    
## 4 n        39    75 x
```

## Using map to create named lists


```r
fn <- function(x) {
  paste0('test-',x)
}
input <- LETTERS[1:3]
input %>% set_names() %>% map(fn)
```

```
## $A
## [1] "test-A"
## 
## $B
## [1] "test-B"
## 
## $C
## [1] "test-C"
```

## Using rename_with to rename specific columns 

Occasionally, you'll only want to rename certain columns and the `rename_with` function offers this capability.


```r
df <- tibble(
  maa_1 = sample(letters, 4),
  maa_2 = sample(1:100, 4),
  ma_3 = sample(1:100, 4),
  ma_4 = sample(letters, 4)
)

df %>% 
  rename_with(~str_replace_all(., "maa", "ma"), contains("maa"))
```

```
## # A tibble: 4 × 4
##   ma_1   ma_2  ma_3 ma_4 
##   <chr> <int> <int> <chr>
## 1 n        69    72 d    
## 2 h        80    51 j    
## 3 p        56    95 g    
## 4 y        20    50 r
```

## piping and dplyr verbs with lists and purrr 

Assume a list with data.frames and I want to use a dplyr verb (or apply any function) within it


```r
list_object %>% 
  map(~ clean_names(.)) %>% 
  map(~ mutate(., new_var = colA + colB))
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
## 1 z           76856 r           31018
## 2 k          397954 v           53471
## 3 w          403952 s           14812
## 4 n          343763 n           32075
## 5 l          282357 u           18862
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
## 1 1441807 576722.8----> 133628      53451. 129546      51818.
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
## t = 13.353, df = 198, p-value < 2.2e-16
## alternative hypothesis: true difference in means between group blue and group green is not equal to 0
## 95 percent confidence interval:
##   7.942389 10.694722
## sample estimates:
##  mean in group blue mean in group green 
##            59.68944            50.37088
```

## Piping into a cor.test

### Method 1 (no group_by)
Using the [exposition pipe](https://magrittr.tidyverse.org/reference/exposition.html) from the `magrittr` package.

```r
library(magrittr)
mtcars %$%
  cor.test(mpg, hp) %>% 
  broom::tidy()
```

```
## # A tibble: 1 × 8
##   estimate statistic     p.value parameter conf.low conf.high method     alter…¹
##      <dbl>     <dbl>       <dbl>     <int>    <dbl>     <dbl> <chr>      <chr>  
## 1   -0.776     -6.74 0.000000179        30   -0.885    -0.586 Pearson's… two.si…
## # … with abbreviated variable name ¹​alternative
```

### Method 2 (allows group_by)
Using a `nest-map-unnest` workflow:


```r
mtcars %>% 
  nest(data = -cyl) %>% 
  mutate(test = map(data, ~ cor.test(.x$mpg, .x$hp)), # S3 list-col
    tidied = map(test, broom::tidy)
  ) %>% 
  unnest(tidied)
```

```
## # A tibble: 3 × 11
##     cyl data     test    estimate stati…¹ p.value param…² conf.…³ conf.…⁴ method
##   <dbl> <list>   <list>     <dbl>   <dbl>   <dbl>   <int>   <dbl>   <dbl> <chr> 
## 1     6 <tibble> <htest>   -0.127  -0.286  0.786        5  -0.803   0.692 Pears…
## 2     4 <tibble> <htest>   -0.524  -1.84   0.0984       9  -0.855   0.111 Pears…
## 3     8 <tibble> <htest>   -0.284  -1.02   0.326       12  -0.708   0.291 Pears…
## # … with 1 more variable: alternative <chr>, and abbreviated variable names
## #   ¹​statistic, ²​parameter, ³​conf.low, ⁴​conf.high
## # ℹ Use `colnames()` to see all variable names
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
## # ℹ Use `print(n = ...)` to see more rows
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

## Normalize function

This has nothing to do with the tidyverse, but just want to put it here


```r
norm <- function(x) (x - min(x)) / (max(x) - min(x))
```

