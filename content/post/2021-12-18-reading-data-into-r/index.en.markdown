---
title: Reading data into R
author: twg
date: '2021-12-18'
slug: reading-data-into-r
categories:
  - data ingest
  - purrr
tags:
  - data ingest
  - purrr
subtitle: ''
summary: 'Methods to get data into R'
authors: []
lastmod: '2021-12-18T09:23:05-08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---



## Reading in multiple files

Using the `purrr` package with csv. If excel, just change read function to `readxl::read_excel` 


```r
data_path <- "path_to_data"   # path to the data
files <- dir(data_path, pattern = "*.csv") # get file names

data <- files %>%
  # read in all the files, appending the path before the filename
  map_dfr(~ read_csv(file.path(data_path, .)))
data
```

## Reading in multiple sheets from single workbook 


```r
source <- "../data/file_name.xlsx"
# Extract the sheet names
sheets <- readxl::excel_sheets(source) 
# iterate through each, assign name of sheet raead
sheets %>%
  purrr::map(function(sheet){ # iterate through each sheet name
  assign(x = sheet,
         value = readxl::read_xlsx(path = source, sheet = sheet),
         envir = .GlobalEnv)
})
```


## Built in data sets 

There are so many packages that come with datasets, it can be hard to keep track of all of them. While there isn't any one site that keeps track of all of them, Vincent Arel-Bundock has built out a pretty detailed composite of a lot of them [here](https://vincentarelbundock.github.io/Rdatasets/datasets.html). And for any package in R, you can see the datasets included using any of the methods outlined [here](https://taylorgrant.netlify.app/post/general-tips-for-r/#getting-the-names-of-datasets-within-a-package).

## Reading in csv from online

Straightforward. Remember if it's from github to use the url to the raw data. 


```r
read_csv("https://raw.githubusercontent.com/fivethirtyeight/nfl-elo-game/master/data/nfl_games.csv") %>% tail()
```

```
## # A tibble: 6 × 12
##   date       season neutral playoff team1 team2  elo1  elo2 elo_prob1 score1
##   <date>      <dbl>   <dbl>   <dbl> <chr> <chr> <dbl> <dbl>     <dbl>  <dbl>
## 1 2021-01-16   2020       0       1 BUF   BAL   1701. 1676.     0.626     17
## 2 2021-01-17   2020       0       1 KC    CLE   1713. 1552.     0.786     22
## 3 2021-01-17   2020       0       1 NO    TB    1704. 1645.     0.671     20
## 4 2021-01-24   2020       0       1 GB    TB    1716. 1679.     0.642     26
## 5 2021-01-24   2020       0       1 KC    BUF   1720. 1720.     0.592     38
## 6 2021-02-07   2020       1       1 TB    KC    1703. 1741.     0.446     31
## # … with 2 more variables: score2 <dbl>, result1 <dbl>
```

## Reading in xls or xlsx from online 

For excel, use the functionality of the `httr` package to write to a tempfile and then read in  


```r
url <- "https://www2.census.gov/programs-surveys/demo/tables/geographic-mobility/2019/state-to-state-migration/State_to_State_Migrations_Table_2019.xls"
httr::GET(url, httr::write_disk(tf <- tempfile(fileext = ".xls")))
```

```
## Response [https://www2.census.gov/programs-surveys/demo/tables/geographic-mobility/2019/state-to-state-migration/State_to_State_Migrations_Table_2019.xls]
##   Date: 2021-12-18 18:17
##   Status: 200
##   Content-Type: application/vnd.ms-excel
##   Size: 104 kB
## <ON DISK>  /var/folders/2t/lq9n3cqx25v_9gp6ztly1z3h0000gn/T//RtmpqYwuZG/file154fb5fb41b63.xls
```

```r
df <- readxl::read_excel(tf,skip=6)

head(df)
```

```
## # A tibble: 6 × 130
##   ...1   ...2  ...3  ...4  ...5  ...6  ...7  Total...8 ...9  Alabama ...11 ...12
##   <chr>  <chr> <chr> <chr> <chr> <chr> <chr> <chr>     <chr> <chr>   <chr> <chr>
## 1 <NA>   Esti… MOE   Esti… MOE   Esti… MOE   Estimate  MOE   Estima… MOE   <NA> 
## 2 <NA>   <NA>  <NA>  <NA>  <NA>  <NA>  <NA>  <NA>      <NA>  <NA>    <NA>  <NA> 
## 3 Unite… 3246… 30094 2803… 1950… 3501… 1780… 7398337   64485 98704   8472  Unit…
## 4 <NA>   <NA>  <NA>  <NA>  <NA>  <NA>  <NA>  <NA>      <NA>  <NA>    <NA>  <NA> 
## 5 Alaba… 4849… 3840  4197… 21706 5348… 20556 104780    8007  N/A     N/A   Alab…
## 6 Alaska 7220… 1360  6093… 7734  74375 6681  34031     4202  260     246   Alas…
## # … with 118 more variables: Alaska <chr>, ...14 <chr>, Arizona <chr>,
## #   ...16 <chr>, Arkansas <chr>, ...18 <chr>, California <chr>, ...20 <chr>,
## #   Colorado <chr>, ...22 <chr>, ...23 <chr>, Connecticut <chr>, ...25 <chr>,
## #   Delaware <chr>, ...27 <chr>, District of Columbia <chr>, ...29 <chr>,
## #   Florida <chr>, ...31 <chr>, Georgia <chr>, ...33 <chr>, ...34 <chr>,
## #   Hawaii <chr>, ...36 <chr>, Idaho <chr>, ...38 <chr>, Illinois <chr>,
## #   ...40 <chr>, Indiana <chr>, ...42 <chr>, Iowa <chr>, ...44 <chr>, …
```

## Reading in a zip file


```r
# create temporary directory for data
ifelse(!dir.exists("data/unzipped"), dir.create("data/unzipped"), "Directory already exists...")
# specify zip URL 
url <- "http://seanlahman.com/files/database/baseballdatabank-2017.1.zip"
# put zip contents into temp file
tmp <- tempfile()
download.file(url, destfile = tmp)
unzip(tmp, exdir = "data/unzipped/.")
```

We can keep the unzipped contents in a separate folder if the data is large and time consuming to download. 


```r
path_unzip <- "data/unzipped/data_archive.zip"
ifelse(!file.exists(path_unzip), 
       download.file(url, path_unzip, mode = "wb"),
       "file already exists")
```

