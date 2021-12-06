---
title: Tidy Data
author: twg
date: '2021-12-05'
slug: tidy-data
categories:
  - R
  - tidy
  - tidyverse
  - pivot
tags:
  - R
  - tidy
  - tidyverse
  - pivot
subtitle: ''
summary: 'Getting data into tidy format - both long and wide'
authors: []
lastmod: '2021-12-05T22:12:02-08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---





# pivoting

`gather` and `spread` are being replaced by the `pivot` function. How it works...

## pivot_longer() 

### String data in column names

This feels very much like the old `gather()` function. 

```r
head(relig_income)
```

```
## # A tibble: 6 × 11
##   religion           `<$10k` `$10-20k` `$20-30k` `$30-40k` `$40-50k` `$50-75k` `$75-100k` `$100-150k` `>150k`
##   <chr>                <dbl>     <dbl>     <dbl>     <dbl>     <dbl>     <dbl>      <dbl>       <dbl>   <dbl>
## 1 Agnostic                27        34        60        81        76       137        122         109      84
## 2 Atheist                 12        27        37        52        35        70         73          59      74
## 3 Buddhist                27        21        30        34        33        58         62          39      53
## 4 Catholic               418       617       732       670       638      1116        949         792     633
## 5 Don’t know/refused      15        14        15        11        10        35         21          17      18
## 6 Evangelical Prot       575       869      1064       982       881      1486        949         723     414
## # … with 1 more variable: Don't know/refused <dbl>
```

```r
relig_income %>%
  pivot_longer(-religion, names_to = "income", values_to = "count")
```

```
## # A tibble: 180 × 3
##    religion income             count
##    <chr>    <chr>              <dbl>
##  1 Agnostic <$10k                 27
##  2 Agnostic $10-20k               34
##  3 Agnostic $20-30k               60
##  4 Agnostic $30-40k               81
##  5 Agnostic $40-50k               76
##  6 Agnostic $50-75k              137
##  7 Agnostic $75-100k             122
##  8 Agnostic $100-150k            109
##  9 Agnostic >150k                 84
## 10 Agnostic Don't know/refused    96
## # … with 170 more rows
```

### Numeric data in column names

Now, here's where you can start to see the power of the function. We grab the columns with a select helper, give it a name, and then the `names_prefix` identifies what to drop from the new variable. And we can automatically drop NAs. 


```r
head(billboard)
```

```
## # A tibble: 6 × 79
##   artist   track    date.entered   wk1   wk2   wk3   wk4   wk5   wk6   wk7   wk8   wk9  wk10  wk11  wk12  wk13
##   <chr>    <chr>    <date>       <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
## 1 2 Pac    Baby Do… 2000-02-26      87    82    72    77    87    94    99    NA    NA    NA    NA    NA    NA
## 2 2Ge+her  The Har… 2000-09-02      91    87    92    NA    NA    NA    NA    NA    NA    NA    NA    NA    NA
## 3 3 Doors… Krypton… 2000-04-08      81    70    68    67    66    57    54    53    51    51    51    51    47
## 4 3 Doors… Loser    2000-10-21      76    76    72    69    67    65    55    59    62    61    61    59    61
## 5 504 Boyz Wobble … 2000-04-15      57    34    25    17    17    31    36    49    53    57    64    70    75
## 6 98^0     Give Me… 2000-08-19      51    39    34    26    26    19     2     2     3     6     7    22    29
## # … with 63 more variables: wk14 <dbl>, wk15 <dbl>, wk16 <dbl>, wk17 <dbl>, wk18 <dbl>, wk19 <dbl>,
## #   wk20 <dbl>, wk21 <dbl>, wk22 <dbl>, wk23 <dbl>, wk24 <dbl>, wk25 <dbl>, wk26 <dbl>, wk27 <dbl>,
## #   wk28 <dbl>, wk29 <dbl>, wk30 <dbl>, wk31 <dbl>, wk32 <dbl>, wk33 <dbl>, wk34 <dbl>, wk35 <dbl>,
## #   wk36 <dbl>, wk37 <dbl>, wk38 <dbl>, wk39 <dbl>, wk40 <dbl>, wk41 <dbl>, wk42 <dbl>, wk43 <dbl>,
## #   wk44 <dbl>, wk45 <dbl>, wk46 <dbl>, wk47 <dbl>, wk48 <dbl>, wk49 <dbl>, wk50 <dbl>, wk51 <dbl>,
## #   wk52 <dbl>, wk53 <dbl>, wk54 <dbl>, wk55 <dbl>, wk56 <dbl>, wk57 <dbl>, wk58 <dbl>, wk59 <dbl>,
## #   wk60 <dbl>, wk61 <dbl>, wk62 <dbl>, wk63 <dbl>, wk64 <dbl>, wk65 <dbl>, wk66 <lgl>, wk67 <lgl>, …
```

```r
billboard %>%
  pivot_longer(
    cols = starts_with("wk"),
    names_to = "week",
    names_prefix = "wk",
    values_to = "rank",
    values_drop_na = TRUE
  )
```

```
## # A tibble: 5,307 × 5
##    artist  track                   date.entered week   rank
##    <chr>   <chr>                   <date>       <chr> <dbl>
##  1 2 Pac   Baby Don't Cry (Keep... 2000-02-26   1        87
##  2 2 Pac   Baby Don't Cry (Keep... 2000-02-26   2        82
##  3 2 Pac   Baby Don't Cry (Keep... 2000-02-26   3        72
##  4 2 Pac   Baby Don't Cry (Keep... 2000-02-26   4        77
##  5 2 Pac   Baby Don't Cry (Keep... 2000-02-26   5        87
##  6 2 Pac   Baby Don't Cry (Keep... 2000-02-26   6        94
##  7 2 Pac   Baby Don't Cry (Keep... 2000-02-26   7        99
##  8 2Ge+her The Hardest Part Of ... 2000-09-02   1        91
##  9 2Ge+her The Hardest Part Of ... 2000-09-02   2        87
## 10 2Ge+her The Hardest Part Of ... 2000-09-02   3        92
## # … with 5,297 more rows
```

### Many variables in column names

Even more complex, here we're taking multiple columns, gathering them, and then creating three new columns of data. The `names_pattern` uses the same specification as `separate()`, in this case regex to split out the data. To understand it, run it first, without the multiple columns, then again with it. 


```r
head(who)
```

```
## # A tibble: 6 × 60
##   country     iso2  iso3   year new_sp_m014 new_sp_m1524 new_sp_m2534 new_sp_m3544 new_sp_m4554 new_sp_m5564
##   <chr>       <chr> <chr> <int>       <int>        <int>        <int>        <int>        <int>        <int>
## 1 Afghanistan AF    AFG    1980          NA           NA           NA           NA           NA           NA
## 2 Afghanistan AF    AFG    1981          NA           NA           NA           NA           NA           NA
## 3 Afghanistan AF    AFG    1982          NA           NA           NA           NA           NA           NA
## 4 Afghanistan AF    AFG    1983          NA           NA           NA           NA           NA           NA
## 5 Afghanistan AF    AFG    1984          NA           NA           NA           NA           NA           NA
## 6 Afghanistan AF    AFG    1985          NA           NA           NA           NA           NA           NA
## # … with 50 more variables: new_sp_m65 <int>, new_sp_f014 <int>, new_sp_f1524 <int>, new_sp_f2534 <int>,
## #   new_sp_f3544 <int>, new_sp_f4554 <int>, new_sp_f5564 <int>, new_sp_f65 <int>, new_sn_m014 <int>,
## #   new_sn_m1524 <int>, new_sn_m2534 <int>, new_sn_m3544 <int>, new_sn_m4554 <int>, new_sn_m5564 <int>,
## #   new_sn_m65 <int>, new_sn_f014 <int>, new_sn_f1524 <int>, new_sn_f2534 <int>, new_sn_f3544 <int>,
## #   new_sn_f4554 <int>, new_sn_f5564 <int>, new_sn_f65 <int>, new_ep_m014 <int>, new_ep_m1524 <int>,
## #   new_ep_m2534 <int>, new_ep_m3544 <int>, new_ep_m4554 <int>, new_ep_m5564 <int>, new_ep_m65 <int>,
## #   new_ep_f014 <int>, new_ep_f1524 <int>, new_ep_f2534 <int>, new_ep_f3544 <int>, new_ep_f4554 <int>, …
```

```r
who %>% pivot_longer(
  cols = new_sp_m014:newrel_f65,
  # names_to = c("diagnosis", "gender", "age"),
  # names_pattern = "new_?(.*)_(.)(.*)",
  values_to = "count"
) %>% sample_n(5)
```

```
## # A tibble: 5 × 6
##   country          iso2  iso3   year name         count
##   <chr>            <chr> <chr> <int> <chr>        <int>
## 1 Greenland        GL    GRL    2004 new_ep_m014     NA
## 2 Estonia          EE    EST    1994 new_sn_m1524    NA
## 3 Pakistan         PK    PAK    2008 new_sn_m65      NA
## 4 China, Macao SAR MO    MAC    1994 new_sn_f65      NA
## 5 Switzerland      CH    CHE    1994 newrel_f2534    NA
```

```r
who %>% pivot_longer(
  cols = new_sp_m014:newrel_f65,
  names_to = c("diagnosis", "gender", "age"),
  names_pattern = "new_?(.*)_(.)(.*)",
  values_to = "count"
) %>% sample_n(5)
```

```
## # A tibble: 5 × 8
##   country  iso2  iso3   year diagnosis gender age   count
##   <chr>    <chr> <chr> <int> <chr>     <chr>  <chr> <int>
## 1 Belarus  BY    BLR    2013 sp        f      2534     NA
## 2 Tokelau  TK    TKL    2004 sn        f      5564     NA
## 3 Georgia  GE    GEO    1999 ep        f      5564     NA
## 4 Anguilla AI    AIA    1998 ep        m      5564     NA
## 5 Nigeria  NG    NGA    2010 ep        f      5564     NA
```

To take it even further, we can identify these newly created variables as factors and define them within the same function using the `names_ptypes` command. 


```r
who %>% pivot_longer(
  cols = new_sp_m014:newrel_f65,
  names_to = c("diagnosis", "gender", "age"), 
  names_pattern = "new_?(.*)_(.)(.*)",
  names_ptypes = list(
    gender = factor(levels = c("f", "m")),
    age = factor(
      levels = c("014", "1524", "2534", "3544", "4554", "5564", "65"), 
      ordered = TRUE
    )
  ),
  values_to = "count",
)
```

```
## # A tibble: 405,440 × 8
##    country     iso2  iso3   year diagnosis gender age   count
##    <chr>       <chr> <chr> <int> <chr>     <fct>  <ord> <int>
##  1 Afghanistan AF    AFG    1980 sp        m      014      NA
##  2 Afghanistan AF    AFG    1980 sp        m      1524     NA
##  3 Afghanistan AF    AFG    1980 sp        m      2534     NA
##  4 Afghanistan AF    AFG    1980 sp        m      3544     NA
##  5 Afghanistan AF    AFG    1980 sp        m      4554     NA
##  6 Afghanistan AF    AFG    1980 sp        m      5564     NA
##  7 Afghanistan AF    AFG    1980 sp        m      65       NA
##  8 Afghanistan AF    AFG    1980 sp        f      014      NA
##  9 Afghanistan AF    AFG    1980 sp        f      1524     NA
## 10 Afghanistan AF    AFG    1980 sp        f      2534     NA
## # … with 405,430 more rows
```

### Multiple observations per row

In the below, each row has two individuals, or sometimes an NA for the second. This gathers all rows except family, and the use ".value"  in the `names_to` command specifies that part of the column name should be a new variable. 


```r
family <- tribble(
  ~family,  ~dob_child1,  ~dob_child2, ~gender_child1, ~gender_child2,
       1L, "1998-11-26", "2000-01-29",             1L,             2L,
       2L, "1996-06-22",           NA,             2L,             NA,
       3L, "2002-07-11", "2004-04-05",             2L,             2L,
       4L, "2004-10-10", "2009-08-27",             1L,             1L,
       5L, "2000-12-05", "2005-02-28",             2L,             1L,
) %>% mutate_at(vars(starts_with("dob")), parse_date)
family
```

```
## # A tibble: 5 × 5
##   family dob_child1 dob_child2 gender_child1 gender_child2
##    <int> <date>     <date>             <int>         <int>
## 1      1 1998-11-26 2000-01-29             1             2
## 2      2 1996-06-22 NA                     2            NA
## 3      3 2002-07-11 2004-04-05             2             2
## 4      4 2004-10-10 2009-08-27             1             1
## 5      5 2000-12-05 2005-02-28             2             1
```

```r
family %>% 
  pivot_longer(
    -family, 
    names_to = c(".value", "child"), 
    names_sep = "_", 
    values_drop_na = TRUE
  )
```

```
## # A tibble: 9 × 4
##   family child  dob        gender
##    <int> <chr>  <date>      <int>
## 1      1 child1 1998-11-26      1
## 2      1 child2 2000-01-29      2
## 3      2 child1 1996-06-22      2
## 4      3 child1 2002-07-11      2
## 5      3 child2 2004-04-05      2
## 6      4 child1 2004-10-10      1
## 7      4 child2 2009-08-27      1
## 8      5 child1 2000-12-05      2
## 9      5 child2 2005-02-28      1
```

Another example of using the ".value" is in the following. .value captures the new variable name and "time" is the numeric identifier. 


```r
pnl <- tibble(
  x = 1:4,
  a = c(1, 1,0, 0),
  b = c(0, 1, 1, 1),
  y1 = rnorm(4),
  y2 = rnorm(4),
  z1 = rep(3, 4),
  z2 = rep(-2, 4),
)
pnl
```

```
## # A tibble: 4 × 7
##       x     a     b     y1     y2    z1    z2
##   <int> <dbl> <dbl>  <dbl>  <dbl> <dbl> <dbl>
## 1     1     1     0 -1.79   1.26      3    -2
## 2     2     1     1 -0.673  0.712     3    -2
## 3     3     0     1 -1.35  -2.54      3    -2
## 4     4     0     1 -0.383 -1.38      3    -2
```

```r
pnl %>% 
  pivot_longer(
    -c(x, a, b), 
    names_to = c(".value", "time"), 
    names_pattern = "(.)(.)"
  )
```

```
## # A tibble: 8 × 6
##       x     a     b time       y     z
##   <int> <dbl> <dbl> <chr>  <dbl> <dbl>
## 1     1     1     0 1     -1.79      3
## 2     1     1     0 2      1.26     -2
## 3     2     1     1 1     -0.673     3
## 4     2     1     1 2      0.712    -2
## 5     3     0     1 1     -1.35      3
## 6     3     0     1 2     -2.54     -2
## 7     4     0     1 1     -0.383     3
## 8     4     0     1 2     -1.38     -2
```

## pivot_wider()

Data capturing anytime a fish passes through a station. A row is only added if a fish was seen, so often times there will be NAs. If we widen the data so that each station is a column, the NAs are apparent. The NAs can be filled within the function using the `values_fill` command. 

```r
fish_encounters
```

```
## # A tibble: 114 × 3
##    fish  station  seen
##    <fct> <fct>   <int>
##  1 4842  Release     1
##  2 4842  I80_1       1
##  3 4842  Lisbon      1
##  4 4842  Rstr        1
##  5 4842  Base_TD     1
##  6 4842  BCE         1
##  7 4842  BCW         1
##  8 4842  BCE2        1
##  9 4842  BCW2        1
## 10 4842  MAE         1
## # … with 104 more rows
```

```r
fish_encounters %>% pivot_wider(
  names_from = station, 
  values_from = seen,
  # values_fill = list(seen = 0)
) %>% head()
```

```
## # A tibble: 6 × 12
##   fish  Release I80_1 Lisbon  Rstr Base_TD   BCE   BCW  BCE2  BCW2   MAE   MAW
##   <fct>   <int> <int>  <int> <int>   <int> <int> <int> <int> <int> <int> <int>
## 1 4842        1     1      1     1       1     1     1     1     1     1     1
## 2 4843        1     1      1     1       1     1     1     1     1     1     1
## 3 4844        1     1      1     1       1     1     1     1     1     1     1
## 4 4845        1     1      1     1       1    NA    NA    NA    NA    NA    NA
## 5 4847        1     1      1    NA      NA    NA    NA    NA    NA    NA    NA
## 6 4848        1     1      1     1      NA    NA    NA    NA    NA    NA    NA
```

```r
fish_encounters %>% pivot_wider(
  names_from = station, 
  values_from = seen,
  values_fill = list(seen = 0)
) %>% head()
```

```
## # A tibble: 6 × 12
##   fish  Release I80_1 Lisbon  Rstr Base_TD   BCE   BCW  BCE2  BCW2   MAE   MAW
##   <fct>   <int> <int>  <int> <int>   <int> <int> <int> <int> <int> <int> <int>
## 1 4842        1     1      1     1       1     1     1     1     1     1     1
## 2 4843        1     1      1     1       1     1     1     1     1     1     1
## 3 4844        1     1      1     1       1     1     1     1     1     1     1
## 4 4845        1     1      1     1       1     0     0     0     0     0     0
## 5 4847        1     1      1     0       0     0     0     0     0     0     0
## 6 4848        1     1      1     1       0     0     0     0     0     0     0
```

### Aggregation 

This is actually sort of bonkers. We can spread data and aggregate it, all at the same time. The warpbreaks dataset captures 9 experiments for 2 different types of wool and 3 levels of tension. There is no unique way of spreading the data. 


```r
warpbreaks <- warpbreaks %>% as_tibble() %>% select(wool, tension, breaks)
warpbreaks
```

```
## # A tibble: 54 × 3
##    wool  tension breaks
##    <fct> <fct>    <dbl>
##  1 A     L           26
##  2 A     L           30
##  3 A     L           54
##  4 A     L           25
##  5 A     L           70
##  6 A     L           52
##  7 A     L           51
##  8 A     L           26
##  9 A     L           67
## 10 A     M           18
## # … with 44 more rows
```

```r
warpbreaks %>% count(wool, tension)
```

```
## # A tibble: 6 × 3
##   wool  tension     n
##   <fct> <fct>   <int>
## 1 A     L           9
## 2 A     M           9
## 3 A     H           9
## 4 B     L           9
## 5 B     M           9
## 6 B     H           9
```

```r
warpbreaks %>% pivot_wider(names_from = wool, values_from = breaks)
```

```
## Warning: Values are not uniquely identified; output will contain list-cols.
## * Use `values_fn = list` to suppress this warning.
## * Use `values_fn = length` to identify where the duplicates arise
## * Use `values_fn = {summary_fun}` to summarise duplicates
```

```
## # A tibble: 3 × 3
##   tension A         B        
##   <fct>   <list>    <list>   
## 1 L       <dbl [9]> <dbl [9]>
## 2 M       <dbl [9]> <dbl [9]>
## 3 H       <dbl [9]> <dbl [9]>
```

But we can use `pivot_wider()` to spread the data and then to aggregate each wool and tension to the average. 

```r
warpbreaks %>% 
  pivot_wider(
    names_from = wool, 
    values_from = breaks,
    values_fn = list(breaks = mean)
  )
```

```
## # A tibble: 3 × 3
##   tension     A     B
##   <fct>   <dbl> <dbl>
## 1 L        44.6  28.2
## 2 M        24    28.8
## 3 H        24.6  18.8
```

### Generate column name from multiple variables

Data with a country and production levels of specific products. What if wanted a column for each product *and* country?  

```r
production <- expand_grid(
    product = c("A", "B"), 
    country = c("AI", "EI"), 
    year = 2000:2014
  ) %>%
  filter((product == "A" & country == "AI") | product == "B") %>% 
  mutate(production = rnorm(nrow(.)))
production
```

```
## # A tibble: 45 × 4
##    product country  year production
##    <chr>   <chr>   <int>      <dbl>
##  1 A       AI       2000      0.831
##  2 A       AI       2001     -0.156
##  3 A       AI       2002     -2.11 
##  4 A       AI       2003     -0.987
##  5 A       AI       2004      1.34 
##  6 A       AI       2005      0.497
##  7 A       AI       2006     -1.81 
##  8 A       AI       2007      1.88 
##  9 A       AI       2008     -0.108
## 10 A       AI       2009     -1.22 
## # … with 35 more rows
```

By specifying multiple `names_from` we can unite them into single columns. 


```r
production %>% pivot_wider(
  names_from = c(product, country), 
  values_from = production
)
```

```
## # A tibble: 15 × 4
##     year   A_AI     B_AI   B_EI
##    <int>  <dbl>    <dbl>  <dbl>
##  1  2000  0.831 -0.935    0.462
##  2  2001 -0.156  1.30    -0.354
##  3  2002 -2.11  -0.706   -1.29 
##  4  2003 -0.987 -0.583    0.150
##  5  2004  1.34  -0.119    0.251
##  6  2005  0.497 -1.53     0.688
##  7  2006 -1.81   0.985   -1.11 
##  8  2007  1.88   0.216   -1.35 
##  9  2008 -0.108  0.464   -0.607
## 10  2009 -1.22   0.802    0.538
## 11  2010 -1.01  -1.67    -0.505
## 12  2011 -1.27  -0.199    0.583
## 13  2012 -1.41   0.267    1.06 
## 14  2013 -1.02   0.590   -0.144
## 15  2014  0.200  0.00375 -0.169
```

### Tidy Census data

This looks very much like a standard use case of `spread()`. 

```r
us_rent_income
```

```
## # A tibble: 104 × 5
##    GEOID NAME       variable estimate   moe
##    <chr> <chr>      <chr>       <dbl> <dbl>
##  1 01    Alabama    income      24476   136
##  2 01    Alabama    rent          747     3
##  3 02    Alaska     income      32940   508
##  4 02    Alaska     rent         1200    13
##  5 04    Arizona    income      27517   148
##  6 04    Arizona    rent          972     4
##  7 05    Arkansas   income      23789   165
##  8 05    Arkansas   rent          709     5
##  9 06    California income      29454   109
## 10 06    California rent         1358     3
## # … with 94 more rows
```

```r
us_rent_income %>% 
  pivot_wider(names_from = variable, values_from = c(estimate, moe))
```

```
## # A tibble: 52 × 6
##    GEOID NAME                 estimate_income estimate_rent moe_income moe_rent
##    <chr> <chr>                          <dbl>         <dbl>      <dbl>    <dbl>
##  1 01    Alabama                        24476           747        136        3
##  2 02    Alaska                         32940          1200        508       13
##  3 04    Arizona                        27517           972        148        4
##  4 05    Arkansas                       23789           709        165        5
##  5 06    California                     29454          1358        109        3
##  6 08    Colorado                       32401          1125        109        5
##  7 09    Connecticut                    35326          1123        195        5
##  8 10    Delaware                       31560          1076        247       10
##  9 11    District of Columbia           43198          1424        681       17
## 10 12    Florida                        25952          1077         70        3
## # … with 42 more rows
```

### Contact List 


```r
contacts <- tribble(
  ~field, ~value,
  "name", "Jiena McLellan",
  "company", "Toyota", 
  "name", "John Smith", 
  "company", "google", 
  "email", "john@google.com",
  "name", "Huxley Ratcliffe"
)
contacts
```

```
## # A tibble: 6 × 2
##   field   value           
##   <chr>   <chr>           
## 1 name    Jiena McLellan  
## 2 company Toyota          
## 3 name    John Smith      
## 4 company google          
## 5 email   john@google.com 
## 6 name    Huxley Ratcliffe
```

There is no unique identifier for each person, so we have to create one. And from there we can then spread it all out. 


```r
contacts <- contacts %>% 
  mutate(
    person_id = cumsum(field == "name")
  )
contacts
```

```
## # A tibble: 6 × 3
##   field   value            person_id
##   <chr>   <chr>                <int>
## 1 name    Jiena McLellan           1
## 2 company Toyota                   1
## 3 name    John Smith               2
## 4 company google                   2
## 5 email   john@google.com          2
## 6 name    Huxley Ratcliffe         3
```

```r
contacts %>% 
  pivot_wider(names_from = field, values_from = value)
```

```
## # A tibble: 3 × 4
##   person_id name             company email          
##       <int> <chr>            <chr>   <chr>          
## 1         1 Jiena McLellan   Toyota  <NA>           
## 2         2 John Smith       google  john@google.com
## 3         3 Huxley Ratcliffe <NA>    <NA>
```

## Longer, then Wider 

### World Bank data 

The goal is to produce a tidy dataset where each variable is a column. 


```r
world_bank_pop
```

```
## # A tibble: 1,056 × 20
##    country indicator  `2000` `2001` `2002` `2003`  `2004`  `2005`   `2006`   `2007`   `2008`   `2009`   `2010`
##    <chr>   <chr>       <dbl>  <dbl>  <dbl>  <dbl>   <dbl>   <dbl>    <dbl>    <dbl>    <dbl>    <dbl>    <dbl>
##  1 ABW     SP.URB.TO… 4.24e4 4.30e4 4.37e4 4.42e4 4.47e+4 4.49e+4  4.49e+4  4.47e+4  4.44e+4  4.41e+4  4.38e+4
##  2 ABW     SP.URB.GR… 1.18e0 1.41e0 1.43e0 1.31e0 9.51e-1 4.91e-1 -1.78e-2 -4.35e-1 -6.98e-1 -7.31e-1 -6.24e-1
##  3 ABW     SP.POP.TO… 9.09e4 9.29e4 9.50e4 9.70e4 9.87e+4 1.00e+5  1.01e+5  1.01e+5  1.01e+5  1.01e+5  1.02e+5
##  4 ABW     SP.POP.GR… 2.06e0 2.23e0 2.23e0 2.11e0 1.76e+0 1.30e+0  7.98e-1  3.84e-1  1.31e-1  9.86e-2  2.13e-1
##  5 AFG     SP.URB.TO… 4.44e6 4.65e6 4.89e6 5.16e6 5.43e+6 5.69e+6  5.93e+6  6.15e+6  6.36e+6  6.59e+6  6.84e+6
##  6 AFG     SP.URB.GR… 3.91e0 4.66e0 5.13e0 5.23e0 5.12e+0 4.77e+0  4.12e+0  3.65e+0  3.40e+0  3.46e+0  3.70e+0
##  7 AFG     SP.POP.TO… 2.01e7 2.10e7 2.20e7 2.31e7 2.41e+7 2.51e+7  2.59e+7  2.66e+7  2.73e+7  2.80e+7  2.88e+7
##  8 AFG     SP.POP.GR… 3.49e0 4.25e0 4.72e0 4.82e0 4.47e+0 3.87e+0  3.23e+0  2.76e+0  2.51e+0  2.57e+0  2.81e+0
##  9 AGO     SP.URB.TO… 8.23e6 8.71e6 9.22e6 9.77e6 1.03e+7 1.09e+7  1.15e+7  1.21e+7  1.27e+7  1.33e+7  1.40e+7
## 10 AGO     SP.URB.GR… 5.44e0 5.59e0 5.70e0 5.76e0 5.75e+0 5.69e+0  4.92e+0  4.89e+0  4.87e+0  4.85e+0  4.83e+0
## # … with 1,046 more rows, and 7 more variables: 2011 <dbl>, 2012 <dbl>, 2013 <dbl>, 2014 <dbl>, 2015 <dbl>,
## #   2016 <dbl>, 2017 <dbl>
```

First, use `pivot_longer()` to start. 


```r
pop2 <- world_bank_pop %>% 
  pivot_longer(`2000`:`2017`, names_to = "year", values_to = "value")
pop2
```

```
## # A tibble: 19,008 × 4
##    country indicator   year  value
##    <chr>   <chr>       <chr> <dbl>
##  1 ABW     SP.URB.TOTL 2000  42444
##  2 ABW     SP.URB.TOTL 2001  43048
##  3 ABW     SP.URB.TOTL 2002  43670
##  4 ABW     SP.URB.TOTL 2003  44246
##  5 ABW     SP.URB.TOTL 2004  44669
##  6 ABW     SP.URB.TOTL 2005  44889
##  7 ABW     SP.URB.TOTL 2006  44881
##  8 ABW     SP.URB.TOTL 2007  44686
##  9 ABW     SP.URB.TOTL 2008  44375
## 10 ABW     SP.URB.TOTL 2009  44052
## # … with 18,998 more rows
```
But we are still left with multiple "indicator" variables per country. So now we have to separate. 


```r
pop3 <- pop2 %>% 
  separate(indicator, c(NA, "area", "variable"))
pop3
```

```
## # A tibble: 19,008 × 5
##    country area  variable year  value
##    <chr>   <chr> <chr>    <chr> <dbl>
##  1 ABW     URB   TOTL     2000  42444
##  2 ABW     URB   TOTL     2001  43048
##  3 ABW     URB   TOTL     2002  43670
##  4 ABW     URB   TOTL     2003  44246
##  5 ABW     URB   TOTL     2004  44669
##  6 ABW     URB   TOTL     2005  44889
##  7 ABW     URB   TOTL     2006  44881
##  8 ABW     URB   TOTL     2007  44686
##  9 ABW     URB   TOTL     2008  44375
## 10 ABW     URB   TOTL     2009  44052
## # … with 18,998 more rows
```

Now we can widen the data. 


```r
pop3 %>% 
  pivot_wider(names_from = variable, values_from = value)
```

```
## # A tibble: 9,504 × 5
##    country area  year   TOTL    GROW
##    <chr>   <chr> <chr> <dbl>   <dbl>
##  1 ABW     URB   2000  42444  1.18  
##  2 ABW     URB   2001  43048  1.41  
##  3 ABW     URB   2002  43670  1.43  
##  4 ABW     URB   2003  44246  1.31  
##  5 ABW     URB   2004  44669  0.951 
##  6 ABW     URB   2005  44889  0.491 
##  7 ABW     URB   2006  44881 -0.0178
##  8 ABW     URB   2007  44686 -0.435 
##  9 ABW     URB   2008  44375 -0.698 
## 10 ABW     URB   2009  44052 -0.731 
## # … with 9,494 more rows
```

### Multiple Choice data 


```r
multi <- tribble(
  ~id, ~choice1, ~choice2, ~choice3,
  1, "A", "B", "C",
  2, "C", "B",  NA,
  3, "D",  NA,  NA,
  4, "B", "D",  NA
)
multi
```

```
## # A tibble: 4 × 4
##      id choice1 choice2 choice3
##   <dbl> <chr>   <chr>   <chr>  
## 1     1 A       B       C      
## 2     2 C       B       <NA>   
## 3     3 D       <NA>    <NA>   
## 4     4 B       D       <NA>
```

First, gather it with `pivot_longer()` dropping the NAs for people with fewere than 3 choices.  

```r
multi2 <- multi %>% 
  pivot_longer(-id, values_drop_na = TRUE) %>% 
  mutate(checked = TRUE)
multi2
```

```
## # A tibble: 8 × 4
##      id name    value checked
##   <dbl> <chr>   <chr> <lgl>  
## 1     1 choice1 A     TRUE   
## 2     1 choice2 B     TRUE   
## 3     1 choice3 C     TRUE   
## 4     2 choice1 C     TRUE   
## 5     2 choice2 B     TRUE   
## 6     3 choice1 D     TRUE   
## 7     4 choice1 B     TRUE   
## 8     4 choice2 D     TRUE
```
Then widen the data 

```r
multi2 %>% 
  pivot_wider(
    id_cols = id,
    names_from = value, 
    values_from = checked, 
    values_fill = list(checked = FALSE)
  )
```

```
## # A tibble: 4 × 5
##      id A     B     C     D    
##   <dbl> <lgl> <lgl> <lgl> <lgl>
## 1     1 TRUE  TRUE  TRUE  FALSE
## 2     2 FALSE TRUE  TRUE  FALSE
## 3     3 FALSE FALSE FALSE TRUE 
## 4     4 FALSE TRUE  FALSE TRUE
```

## separate()

`separate` works when a dataframe includes compound variables - for instance, age and gender demographics. `separate` works in one of two ways - first, you can pass it a regular expression to split on, but if you choose not to give it, it will default to non-alphanumeric character to split on, as below. 


```r
df <- data.frame(x = c(NA, "m.14", "f.20", "f.45"))
df %>% separate(x, c("Gender", "Age"))
```

```
##   Gender  Age
## 1   <NA> <NA>
## 2      m   14
## 3      f   20
## 4      f   45
```

Second, you can tell it how many characters in to split on. If we change our example somewhat, we can see where this might be useful. 


```r
df <- data.frame(x = c(NA, "m14", "f20", "f45"))
df %>% separate(x, c("Gender", "Age") , 1)
```

```
##   Gender  Age
## 1   <NA> <NA>
## 2      m   14
## 3      f   20
## 4      f   45
```

## unite()

The `unite` function is helpful in that it pulls disparate columns together into one. To see how it works, we'll make some fake data, and then use `unite` to pull the hour, min, and second into a single variable. 


```r
date <- as.Date('2018-01-01') + 0:14
hour <- sample(1:24, 15)
min <- sample(1:60, 15)
second <- sample(1:60, 15)
event <- sample(letters, 15)
data <- data.frame(date, hour, min, second, event)
data
```

```
##          date hour min second event
## 1  2018-01-01   15  51     10     b
## 2  2018-01-02   24  54     47     g
## 3  2018-01-03    9  41     45     k
## 4  2018-01-04    1   6     16     r
## 5  2018-01-05   18  34      7     l
## 6  2018-01-06    6  32     44     d
## 7  2018-01-07   10  29     22     i
## 8  2018-01-08    7  18      6     e
## 9  2018-01-09   22   4     58     t
## 10 2018-01-10   21  42     19     s
## 11 2018-01-11    2  28     36     m
## 12 2018-01-12   23  47     54     p
## 13 2018-01-13   12  17      5     y
## 14 2018-01-14   14  52      2     c
## 15 2018-01-15   19  22      8     u
```

```r
data %>% unite(datetime, hour, min, second, sep = ":")
```

```
##          date datetime event
## 1  2018-01-01 15:51:10     b
## 2  2018-01-02 24:54:47     g
## 3  2018-01-03  9:41:45     k
## 4  2018-01-04   1:6:16     r
## 5  2018-01-05  18:34:7     l
## 6  2018-01-06  6:32:44     d
## 7  2018-01-07 10:29:22     i
## 8  2018-01-08   7:18:6     e
## 9  2018-01-09  22:4:58     t
## 10 2018-01-10 21:42:19     s
## 11 2018-01-11  2:28:36     m
## 12 2018-01-12 23:47:54     p
## 13 2018-01-13  12:17:5     y
## 14 2018-01-14  14:52:2     c
## 15 2018-01-15  19:22:8     u
```

```r
# the default separator is "_", so if that doesn't work, change it as necessary
```

The `unite` function is also helpful when you need to spread multiple columns in a dataframe.


```r
df <- data.frame(quarter=rep(1:4,2),
                 section=rep(c("Sec1", "Sec2"), each=4),
                 QA=c(.9, .7, .6, .8, .6, .9, 1.0, .6),
                 QB=c(.6, .7, .8, .5, .6, .7, .5, .9))

df
```

```
##   quarter section  QA  QB
## 1       1    Sec1 0.9 0.6
## 2       2    Sec1 0.7 0.7
## 3       3    Sec1 0.6 0.8
## 4       4    Sec1 0.8 0.5
## 5       1    Sec2 0.6 0.6
## 6       2    Sec2 0.9 0.7
## 7       3    Sec2 1.0 0.5
## 8       4    Sec2 0.6 0.9
```

What if we want to spread this so that each column relates to a section and a class average on two quizzes each quarter? We would use a lot of what the `tidyr` package has to offer - gather, unite, and then spread. 


```r
df <- data.frame(quarter=rep(1:4,2),
                 section=rep(c("Sec1", "Sec2"), each=4),
                 QA=c(.9, .7, .6, .8, .6, .9, 1.0, .6),
                 QB=c(.6, .7, .8, .5, .6, .7, .5, .9))


df <- df %>% 
  gather(variable, value, -(quarter:section)) %>%
  unite(tmp, section, variable) %>%
  spread(tmp, value)

df
```

```
##   quarter Sec1_QA Sec1_QB Sec2_QA Sec2_QB
## 1       1     0.9     0.6     0.6     0.6
## 2       2     0.7     0.7     0.9     0.7
## 3       3     0.6     0.8     1.0     0.5
## 4       4     0.8     0.5     0.6     0.9
```
