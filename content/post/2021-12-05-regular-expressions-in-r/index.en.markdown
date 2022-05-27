---
title: Regular Expressions in R
author: twg
date: '2022-04-24'
categories:
  - regex
  - strings
tags:
  - regex
  - strings
slug: regular-expressions-in-r
summary: Working with strings and regex
lastmod: '2021-12-05T22:10:16-08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
---





## Extracting text before or after symbol or delimiter


```r
# timestamp as example 
stamp <- "2017-10-16T12:45:04PM"
# time: extract time after the T
gsub(".*\\T", "", stamp) 
```

```
## [1] "12:45:04PM"
```

```r
# date: extract everything before the T
gsub("\\T.*", "", stamp) 
```

```
## [1] "2017-10-16"
```

## Extracting everything up to the Nth occurrence


```r
string <- "abc-def-ghi-jkl-mno"

# up to 1st (not including it) 
sub("^(([^-]*-){0}[^-]*).*", "\\1", string)
```

```
## [1] "abc"
```

```r
# up to the 2nd (not including it)
sub("^(([^-]*-){1}[^-]*).*", "\\1", string)
```

```
## [1] "abc-def"
```

```r
# up to 3rd (not including it)
sub("^(([^-]*-){2}[^-]*).*", "\\1", string)
```

```
## [1] "abc-def-ghi"
```

## Extracting before or after the first occurrence of symbol or delimeter


```r
string <- "THIS_IS_A_STRING_WITH_A-DASH-SECOND-DASH"
# extract everything after first "-"
sub(".*?-", "", string)
```

```
## [1] "DASH-SECOND-DASH"
```

```r
# extract everything up to the first "-"
stringr::str_extract(string, "[^-]+")
```

```
## [1] "THIS_IS_A_STRING_WITH_A"
```

```r
# or using sub
sub("-.*", "", string)
```

```
## [1] "THIS_IS_A_STRING_WITH_A"
```

## Extract the last occurrence in string with multiple occurrences


```r
sub(".*[_]", "", "abc_def_ghi_jkl")
```

```
## [1] "jkl"
```

## Extract everything before last occurrence in string 


```r
sub("_[^_]+$", "", "abc_def_ghi_jkl")
```

```
## [1] "abc_def_ghi"
```

## Extract everything in between two strings


```r
string <- c("The quick brown fox jumped over the lazy dog")
gsub(".*quick (.+) jumped.*", "\\1", string)
```

```
## [1] "brown fox"
```

## Extract hashtags and @handles


```r
string <- c("I can't believe @user said #hashtag and #hashtag2")
str_extract_all(string, "@\\w+")
```

```
## [[1]]
## [1] "@user"
```

```r
str_extract_all(string, "#\\w+")
```

```
## [[1]]
## [1] "#hashtag"  "#hashtag2"
```

## Splitting and pulling data after symbols. 

There are also pure regex ways of achieving the same ends, but `stringr` is so easy. 

```r
library(stringr)
# assume a url with the following path, and we want to pull out the info after the 2nd slash
urlPath <- "/t5/Announcements-and-Info/Bixby-Button-Short-Press-The-choice-is-yours/m-p/168685/highlight/true#M210"

sapply(str_split(urlPath, "/"), "[[", 3)
```

```
## [1] "Announcements-and-Info"
```

## Remove commas and convert string to numeric


```r
to_num <- function(x) { as.numeric(stringi::stri_replace_all_fixed(x, ",", "")) }
stringnum <- "100,954"
to_num(stringnum)
```

```
## [1] 100954
```

## Add commas and convert numeric to string


```r
scales::comma_format()(10000000)
```

```
## [1] "10,000,000"
```

## Extract last N and first N characters in string

The ```str_sub``` function from the ```stringr``` package is useful for this. There are three arguments (string, from = start, to = end) - if you don't include "from" and the "to" argument is positive, then it starts from character 1. If "from" is left out and the "to" argument is negative, it starts from the last character in the string. 


```r
st <- "Thisstringhasnospaces"
# Last word
str_sub(st, -6)
```

```
## [1] "spaces"
```

```r
# First word  
str_sub(st, 1, 4)
```

```
## [1] "This"
```

```r
# In between
str_sub(st, 5, 10)
```

```
## [1] "string"
```

## Programmatically inserting line breaks 

This is convenient when working with lots of data and using `walk()` functions to make lots of graphs. 

```r
pacman::p_load(tidyverse)
dat <- tibble(a = c("Suburban (just outside of urban/metro area)", "Small town (separate from suburban area, but within proximity)", 
"Rural (far from large urban area)", "Urban (close to large metro area)", 
"Other"),
   val = 1:5)

# function to add a line break in front of a word (based on count of spaces)
add_break <- function(x) str_replace_all(x, word(x, str_count(x, "\\S+")/2 + 1), 
                                         paste0("\n", word(x, str_count(x, "\\S+")/2 + 1)))

dat %>% 
  mutate(a = case_when(str_count(a, "\\S+") > 6 ~ add_break(a),
                       TRUE ~ a)) %>%
  ggplot(aes(x = val, y = a)) + 
  geom_point(col = "blue") +
  theme_twg()
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-12-1.png" width="1104" style="display: block; margin: auto;" />

## Counting words in a string

Working with strings and trying to count either words or occurrences. 


```r
library(stringr)
# string with 19 
string <- c("MTD Sales 415  2,667 1  2,014  46 24 52 472  3  2,200  2,256 2,963 25 511  207
            274  14,130")

# this will look for all word characters
str_count(string, '\\w+')
```

```
## [1] 25
```

```r
# the above is counting commas as breaks, so remove the comma count
str_count(string, '\\w+') - str_count(string, ",") 
```

```
## [1] 19
```


## Removing trailing and leading punctuation

In this case, we'll strip out periods before and after.


```r
test <- c('.name.A.','name.B','.name.C.')
gsub('^\\.|\\.$', '', test)
```

```
## [1] "name.A" "name.B" "name.C"
```

## Efficiently converting all versions of a word

Working with text and there are different versions and misspellings of a term


```r
string <- "i was buffereing but then buffered and buffering on the bluff"
# assuming a consistent word stem
str_replace_all(string, "buffer[a-z]+", "buffer")
```

```
## [1] "i was buffer but then buffer and buffer on the bluff"
```

## Splitting words on space or other character


```r
string <- c("apple: banana: orange: kiwi")
str_split(string, boundary('word'))
```

```
## [[1]]
## [1] "apple"  "banana" "orange" "kiwi"
```

## Extracting specific version of a word

Assume we have to find the location of a specific word. In the line below if we search for "string" we're going to get two answers. 

```r
string <- tibble(col = c("strings", "stringer", "word", "part", "string"))
string$col[which(str_detect(string$col, "string"))]
```

```
## [1] "strings"  "stringer" "string"
```

```r
# setting the boundary and find only the exact match
string$col[which(str_detect(string$col, "string\\b"))]
```

```
## [1] "string"
```

