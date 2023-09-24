---
title: Web Scraping
author: twg
date: '2023-09-17'
slug: web-scraping
categories:
  - rvest
  - scraping
tags:
  - rvest
  - scraping
summary: 'Notes on scraping with R'
lastmod: '2023-09-17T14:09:11-07:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---



## Setting headers

A number of sites will reject an rvest session because it looks like a robot. We can use the `httr` pacakge to add headers and start a new session (the `rvest::session()` function is just a wrapper around `httr`). 


```r
headers = c(
    `sec-ch-ua` = '"Chromium";v="116", "Not)A;Brand";v="24", "Google Chrome";v="116"',
    `Referer` = "https://www.google.com/",
    `DNT` = "1",
    `Accept-Language` = "en",
    `sec-ch-ua-mobile` = "?0",
    `User-Agent` = "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.0.0 Safari/537.36",
    `sec-ch-ua-platform` = '"macOS"'
  )

# can do this in two parts or just nest the functions
url <- "https://www.espn.com"
res <- httr::GET(url, httr::add_headers(.headers = headers))
page <- httr:content(res)
# OR
page <- httr::content(httr::GET(url, httr::add_headers(.headers = headers)))
```

## Getting urls on a page

We'll use pgatour.com and we want all links to the complete statistics pages in the Overview section. 


```r
pacman::p_load(tidyverse, rvest)
url <- "https://www.pgatour.com/stats"
page <- rvest::session(url)

# get urls
links <- page |> 
  html_nodes("a") |> # node/nodes has been superseded (just use element/elements)
  html_attr("href") |>
  as_tibble_col(column_name = "path") |> 
  filter(str_detect(path, "detail"))

# get stat types so we know what we're doing
types <- page |> 
  html_elements(".css-1h0wur3") |> 
  html_text()

links |> 
  mutate(stat_type = types)
```

```
## # A tibble: 8 × 2
##   path                stat_type                      
##   <chr>               <chr>                          
## 1 /stats/detail/120   Scoring Average                
## 2 /stats/detail/156   Birdie Average                 
## 3 /stats/detail/02675 SG: Total                      
## 4 /stats/detail/101   Driving Distance               
## 5 /stats/detail/02568 SG: Approach the Green         
## 6 /stats/detail/103   Greens in Regulation Percentage
## 7 /stats/detail/130   Scrambling                     
## 8 /stats/detail/02564 SG: Putting
```

## Fetching data from tables 


```r
pacman::p_load(tidyverse, rvest)
url <- "https://www.goodcarbadcar.net/2023-us-vehicle-sales-figures-by-model/"

page <- session(url)

all_tables <- page |> html_elements("table") 

# sales last month
html_table(all_tables)[[1]]  |> head()
```

```
## # A tibble: 6 × 7
##   Model           Month  `Month LY` Change YTD     `YTD LY` `YTD Change`
##   <chr>           <chr>  <chr>      <chr>  <chr>   <chr>    <chr>       
## 1 Mazda 3         2,611  2,100      24.33  20,732  18,120   14.42       
## 2 Toyota 4Runner  12,569 10,066     24.87  65,224  84,577   -22.88      
## 3 Volvo 60-Series 944    536        76.12  6,005   5,070    18.44       
## 4 Volvo 90-Series 120    99         21.21  1,494   1,320    13.18       
## 5 Honda Accord    17,661 11,619     52.00  135,379 99,340   36.28       
## 6 Subaru Ascent   4,860  5,638      -13.80 42,282  41,058   2.98
```

```r
# quarterly sales
html_table(all_tables)[[2]]  |> head()
```

```
## # A tibble: 6 × 5
##   modelName     `Q2 2023` `Q2 2022` `Year To Date` `Year to Date Previous Year`
##   <chr>         <chr>     <chr>     <chr>          <chr>                       
## 1 Acura ILX     0         2,506     2              6,267                       
## 2 Acura Integra 8,808     1,496     22,050         1,496                       
## 3 Acura MDX     16,741    10,052    40,533         23,610                      
## 4 Acura NSX     0         77        5              123                         
## 5 Acura RDX     8,866     6,361     22,929         14,239                      
## 6 Acura RLX     0         1         0              3
```

Or you can copy the xpath from devtools for a specific table. Since I'm only getting one table here I use `html_element()` rather than the plural "elements." Were I to use `html_elements()` it would return a list.  


```r
pacman::p_load(tidyverse, rvest)
url <- "https://www.goodcarbadcar.net/2023-us-vehicle-sales-figures-by-model/"

page <- session(url)

# sales last month
page |> 
  html_element(xpath = '//*[@id="table_1"]') |> 
  html_table() |> 
  head()
```

```
## # A tibble: 6 × 7
##   Model           Month  `Month LY` Change YTD     `YTD LY` `YTD Change`
##   <chr>           <chr>  <chr>      <chr>  <chr>   <chr>    <chr>       
## 1 Mazda 3         2,611  2,100      24.33  20,732  18,120   14.42       
## 2 Toyota 4Runner  12,569 10,066     24.87  65,224  84,577   -22.88      
## 3 Volvo 60-Series 944    536        76.12  6,005   5,070    18.44       
## 4 Volvo 90-Series 120    99         21.21  1,494   1,320    13.18       
## 5 Honda Accord    17,661 11,619     52.00  135,379 99,340   36.28       
## 6 Subaru Ascent   4,860  5,638      -13.80 42,282  41,058   2.98
```

## Relying on data loaded by javascript

A lot of sites have the data loaded via scripts after the page loads. If the scripts are identifiable, it's often much easier to pull this data (normally in JSON format) then to try to grab specific elements. 

For example, if you went to the PGA Tour's [Scoring Average](https://www.pgatour.com/stats/detail/120) stats page, there is a full table of data that is loaded via javascript. To see which script is loading it, use devtools to Inspect the page, and then use `ctrl+F` to search for a specific number or piece of text on the page. Doing this, you can see that the data is loaded via a script with the id #NEXT_DATA. We can use that to grab our data. 


```r
url <- "https://www.pgatour.com/stats/detail/120"
page <- session(url)
tmp <- page |> 
  html_elements("script#__NEXT_DATA__") |> 
  html_text() |> 
  jsonlite::fromJSON()

# this is where the stats data is found
df <- tmp$props$pageProps$statDetails$rows

# loop over the data to put into tibble format 
overall <- NULL
# create vector 
vct <- 1:nrow(df)
# need to account for the "Tour Average" row 
for (i in vct[!vct %in% which(is.na(df$playerName))]) {
  stat_line <- df$stats |> 
    pluck(i) |> 
    select(-color) |> 
    pivot_wider(names_from = statName, values_from = statValue) |> 
    janitor::clean_names()
  stat <- tibble(rank = df$rank[i], change = df$rankDiff[i], 
                 tendency = df$rankChangeTendency[i],
                 player = df$playerName[i], country = df$country[i], 
                 stat_line) |> 
    mutate(across(c(rank, change, avg, total_adjustment), ~as.numeric(.)),
           change = ifelse(tendency == "DOWN", change*-1, change)) |> 
    select(-tendency)
  overall <- rbind(overall, stat)
}

rbind(head(overall,6),tail(overall,6))
```

```
## # A tibble: 12 × 8
##     rank change player            country            avg total_strokes total_adjustment total_rounds
##    <dbl>  <dbl> <chr>             <chr>            <dbl> <chr>                    <dbl> <chr>       
##  1     1      0 Scottie Scheffler United States     68.6 6,007                    32.3  88          
##  2     2      0 Rory McIlroy      Northern Ireland  68.8 4,411                    -9.24 64          
##  3     3      0 Jon Rahm          Spain             69.0 4,886                    15.6  71          
##  4     4      0 Xander Schauffele United States     69.1 5,313                     6.37 77          
##  5     5      0 Viktor Hovland    Norway            69.1 6,065                    17.8  88          
##  6     6      0 Patrick Cantlay   United States     69.2 4,956                    25.7  72          
##  7   179     -2 Andrew Landry     United States     72.6 4,794                    68.9  67          
##  8   180     -4 Max McGreevy      United States     72.6 5,611                    54.3  78          
##  9   181     -3 Nick Watney       United States     72.7 4,610                    41.6  64          
## 10   182     -2 Ryan Brehm        United States     72.7 5,312                    67.2  74          
## 11   183     -4 Michael Gligic    Canada            72.7 4,383                    54.7  61          
## 12   184     -3 Brandon Matthews  United States     73.1 4,116                    49.7  57
```




