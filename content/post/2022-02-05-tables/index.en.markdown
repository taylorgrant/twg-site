---
title: Tables
author: twg
date: '2022-02-05'
slug: tables
categories:
  - tables
  - DT
  - GT
  - kableExtra
tags:
  - tables
  - DT
  - GT
  - kableExtra
subtitle: ''
summary: 'Building interesting tables with a few different packages'
authors: []
lastmod: '2022-02-05T09:52:17-08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />





Examples of building tables using three packages - [kableExtra](https://cran.r-project.org/web/packages/kableExtra/vignettes/awesome_table_in_html.html), [DT](https://rstudio.github.io/DT/), and [gt](https://gt.rstudio.com/). This will use the top 30 MLB hitters for 2021 from the FanGraphs leaderboard. 

## kableExtra

Starting with kable and kableExtra to make html tables. They're not interactive, but they look nice and require little code.  


```r
mlb %>% 
  head(10) %>%
  kbl(
    # align = "lrc", # justification; by default numeric or right-justified
    # format.args = list(big.mark = ",") # comma formatting if necessary 
    ) %>% 
  kable_styling(bootstrap_options = c("striped", "hover", "responsive")) %>%
  footnote(general = "Source: FanGraphs")
```

<table class="table table-striped table-hover table-responsive" style="margin-left: auto; margin-right: auto;border-bottom: 0;">
 <thead>
  <tr>
   <th style="text-align:left;"> Name </th>
   <th style="text-align:left;"> Team </th>
   <th style="text-align:right;"> G </th>
   <th style="text-align:right;"> PA </th>
   <th style="text-align:right;"> HR </th>
   <th style="text-align:right;"> R </th>
   <th style="text-align:right;"> RBI </th>
   <th style="text-align:right;"> SB </th>
   <th style="text-align:left;"> BB% </th>
   <th style="text-align:left;"> K% </th>
   <th style="text-align:right;"> ISO </th>
   <th style="text-align:right;"> BABIP </th>
   <th style="text-align:right;"> AVG </th>
   <th style="text-align:right;"> OBP </th>
   <th style="text-align:right;"> SLG </th>
   <th style="text-align:right;"> wOBA </th>
   <th style="text-align:right;"> xwOBA </th>
   <th style="text-align:right;"> wRC+ </th>
   <th style="text-align:right;"> BsR </th>
   <th style="text-align:right;"> Off </th>
   <th style="text-align:right;"> Def </th>
   <th style="text-align:right;"> WAR20 </th>
   <th style="text-align:right;"> WAR </th>
   <th style="text-align:right;"> playerid </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Trea Turner </td>
   <td style="text-align:left;"> - - - </td>
   <td style="text-align:right;"> 148 </td>
   <td style="text-align:right;"> 646 </td>
   <td style="text-align:right;"> 28 </td>
   <td style="text-align:right;"> 107 </td>
   <td style="text-align:right;"> 77 </td>
   <td style="text-align:right;"> 32 </td>
   <td style="text-align:left;"> 6.3% </td>
   <td style="text-align:left;"> 17.0% </td>
   <td style="text-align:right;"> 0.208 </td>
   <td style="text-align:right;"> 0.362 </td>
   <td style="text-align:right;"> 0.328 </td>
   <td style="text-align:right;"> 0.375 </td>
   <td style="text-align:right;"> 0.536 </td>
   <td style="text-align:right;"> 0.386 </td>
   <td style="text-align:right;"> 0.362 </td>
   <td style="text-align:right;"> 142 </td>
   <td style="text-align:right;"> 5.0 </td>
   <td style="text-align:right;"> 39.5 </td>
   <td style="text-align:right;"> 7.6 </td>
   <td style="text-align:right;"> 2.7 </td>
   <td style="text-align:right;"> 6.9 </td>
   <td style="text-align:right;"> 16252 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Vladimir Guerrero Jr. </td>
   <td style="text-align:left;"> TOR </td>
   <td style="text-align:right;"> 161 </td>
   <td style="text-align:right;"> 698 </td>
   <td style="text-align:right;"> 48 </td>
   <td style="text-align:right;"> 123 </td>
   <td style="text-align:right;"> 111 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> 12.3% </td>
   <td style="text-align:left;"> 15.8% </td>
   <td style="text-align:right;"> 0.290 </td>
   <td style="text-align:right;"> 0.313 </td>
   <td style="text-align:right;"> 0.311 </td>
   <td style="text-align:right;"> 0.401 </td>
   <td style="text-align:right;"> 0.601 </td>
   <td style="text-align:right;"> 0.419 </td>
   <td style="text-align:right;"> 0.416 </td>
   <td style="text-align:right;"> 166 </td>
   <td style="text-align:right;"> -2.1 </td>
   <td style="text-align:right;"> 54.1 </td>
   <td style="text-align:right;"> -11.1 </td>
   <td style="text-align:right;"> 0.2 </td>
   <td style="text-align:right;"> 6.7 </td>
   <td style="text-align:right;"> 19611 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Bryce Harper </td>
   <td style="text-align:left;"> PHI </td>
   <td style="text-align:right;"> 141 </td>
   <td style="text-align:right;"> 599 </td>
   <td style="text-align:right;"> 35 </td>
   <td style="text-align:right;"> 101 </td>
   <td style="text-align:right;"> 84 </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:left;"> 16.7% </td>
   <td style="text-align:left;"> 22.4% </td>
   <td style="text-align:right;"> 0.305 </td>
   <td style="text-align:right;"> 0.359 </td>
   <td style="text-align:right;"> 0.309 </td>
   <td style="text-align:right;"> 0.429 </td>
   <td style="text-align:right;"> 0.615 </td>
   <td style="text-align:right;"> 0.431 </td>
   <td style="text-align:right;"> 0.430 </td>
   <td style="text-align:right;"> 170 </td>
   <td style="text-align:right;"> -2.5 </td>
   <td style="text-align:right;"> 50.7 </td>
   <td style="text-align:right;"> -4.4 </td>
   <td style="text-align:right;"> 1.6 </td>
   <td style="text-align:right;"> 6.6 </td>
   <td style="text-align:right;"> 11579 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Marcus Semien </td>
   <td style="text-align:left;"> TOR </td>
   <td style="text-align:right;"> 162 </td>
   <td style="text-align:right;"> 724 </td>
   <td style="text-align:right;"> 45 </td>
   <td style="text-align:right;"> 115 </td>
   <td style="text-align:right;"> 102 </td>
   <td style="text-align:right;"> 15 </td>
   <td style="text-align:left;"> 9.1% </td>
   <td style="text-align:left;"> 20.2% </td>
   <td style="text-align:right;"> 0.273 </td>
   <td style="text-align:right;"> 0.276 </td>
   <td style="text-align:right;"> 0.265 </td>
   <td style="text-align:right;"> 0.334 </td>
   <td style="text-align:right;"> 0.538 </td>
   <td style="text-align:right;"> 0.368 </td>
   <td style="text-align:right;"> 0.331 </td>
   <td style="text-align:right;"> 131 </td>
   <td style="text-align:right;"> 4.0 </td>
   <td style="text-align:right;"> 31.5 </td>
   <td style="text-align:right;"> 10.0 </td>
   <td style="text-align:right;"> 1.2 </td>
   <td style="text-align:right;"> 6.6 </td>
   <td style="text-align:right;"> 12533 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Juan Soto </td>
   <td style="text-align:left;"> WSN </td>
   <td style="text-align:right;"> 151 </td>
   <td style="text-align:right;"> 654 </td>
   <td style="text-align:right;"> 29 </td>
   <td style="text-align:right;"> 111 </td>
   <td style="text-align:right;"> 95 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:left;"> 22.2% </td>
   <td style="text-align:left;"> 14.2% </td>
   <td style="text-align:right;"> 0.221 </td>
   <td style="text-align:right;"> 0.332 </td>
   <td style="text-align:right;"> 0.313 </td>
   <td style="text-align:right;"> 0.465 </td>
   <td style="text-align:right;"> 0.534 </td>
   <td style="text-align:right;"> 0.420 </td>
   <td style="text-align:right;"> 0.430 </td>
   <td style="text-align:right;"> 163 </td>
   <td style="text-align:right;"> -3.3 </td>
   <td style="text-align:right;"> 48.8 </td>
   <td style="text-align:right;"> -4.3 </td>
   <td style="text-align:right;"> 2.5 </td>
   <td style="text-align:right;"> 6.6 </td>
   <td style="text-align:right;"> 20123 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Jose Ramirez </td>
   <td style="text-align:left;"> CLE </td>
   <td style="text-align:right;"> 152 </td>
   <td style="text-align:right;"> 636 </td>
   <td style="text-align:right;"> 36 </td>
   <td style="text-align:right;"> 111 </td>
   <td style="text-align:right;"> 103 </td>
   <td style="text-align:right;"> 27 </td>
   <td style="text-align:left;"> 11.3% </td>
   <td style="text-align:left;"> 13.7% </td>
   <td style="text-align:right;"> 0.272 </td>
   <td style="text-align:right;"> 0.256 </td>
   <td style="text-align:right;"> 0.266 </td>
   <td style="text-align:right;"> 0.355 </td>
   <td style="text-align:right;"> 0.538 </td>
   <td style="text-align:right;"> 0.372 </td>
   <td style="text-align:right;"> 0.374 </td>
   <td style="text-align:right;"> 137 </td>
   <td style="text-align:right;"> 6.3 </td>
   <td style="text-align:right;"> 35.4 </td>
   <td style="text-align:right;"> 5.8 </td>
   <td style="text-align:right;"> 3.5 </td>
   <td style="text-align:right;"> 6.3 </td>
   <td style="text-align:right;"> 13510 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Fernando Tatis Jr. </td>
   <td style="text-align:left;"> SDP </td>
   <td style="text-align:right;"> 130 </td>
   <td style="text-align:right;"> 546 </td>
   <td style="text-align:right;"> 42 </td>
   <td style="text-align:right;"> 99 </td>
   <td style="text-align:right;"> 97 </td>
   <td style="text-align:right;"> 25 </td>
   <td style="text-align:left;"> 11.4% </td>
   <td style="text-align:left;"> 28.0% </td>
   <td style="text-align:right;"> 0.328 </td>
   <td style="text-align:right;"> 0.324 </td>
   <td style="text-align:right;"> 0.282 </td>
   <td style="text-align:right;"> 0.364 </td>
   <td style="text-align:right;"> 0.611 </td>
   <td style="text-align:right;"> 0.403 </td>
   <td style="text-align:right;"> 0.406 </td>
   <td style="text-align:right;"> 156 </td>
   <td style="text-align:right;"> 9.3 </td>
   <td style="text-align:right;"> 48.5 </td>
   <td style="text-align:right;"> -5.3 </td>
   <td style="text-align:right;"> 2.9 </td>
   <td style="text-align:right;"> 6.1 </td>
   <td style="text-align:right;"> 19709 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Carlos Correa </td>
   <td style="text-align:left;"> HOU </td>
   <td style="text-align:right;"> 148 </td>
   <td style="text-align:right;"> 640 </td>
   <td style="text-align:right;"> 26 </td>
   <td style="text-align:right;"> 104 </td>
   <td style="text-align:right;"> 92 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:left;"> 11.7% </td>
   <td style="text-align:left;"> 18.1% </td>
   <td style="text-align:right;"> 0.205 </td>
   <td style="text-align:right;"> 0.308 </td>
   <td style="text-align:right;"> 0.279 </td>
   <td style="text-align:right;"> 0.366 </td>
   <td style="text-align:right;"> 0.485 </td>
   <td style="text-align:right;"> 0.364 </td>
   <td style="text-align:right;"> 0.373 </td>
   <td style="text-align:right;"> 134 </td>
   <td style="text-align:right;"> -0.6 </td>
   <td style="text-align:right;"> 26.4 </td>
   <td style="text-align:right;"> 9.6 </td>
   <td style="text-align:right;"> 0.8 </td>
   <td style="text-align:right;"> 5.8 </td>
   <td style="text-align:right;"> 14162 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Starling Marte </td>
   <td style="text-align:left;"> - - - </td>
   <td style="text-align:right;"> 120 </td>
   <td style="text-align:right;"> 526 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 89 </td>
   <td style="text-align:right;"> 55 </td>
   <td style="text-align:right;"> 47 </td>
   <td style="text-align:left;"> 8.2% </td>
   <td style="text-align:left;"> 18.8% </td>
   <td style="text-align:right;"> 0.148 </td>
   <td style="text-align:right;"> 0.372 </td>
   <td style="text-align:right;"> 0.310 </td>
   <td style="text-align:right;"> 0.383 </td>
   <td style="text-align:right;"> 0.458 </td>
   <td style="text-align:right;"> 0.364 </td>
   <td style="text-align:right;"> 0.347 </td>
   <td style="text-align:right;"> 134 </td>
   <td style="text-align:right;"> 12.3 </td>
   <td style="text-align:right;"> 34.7 </td>
   <td style="text-align:right;"> 2.6 </td>
   <td style="text-align:right;"> 1.3 </td>
   <td style="text-align:right;"> 5.5 </td>
   <td style="text-align:right;"> 9241 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Bryan Reynolds </td>
   <td style="text-align:left;"> PIT </td>
   <td style="text-align:right;"> 159 </td>
   <td style="text-align:right;"> 646 </td>
   <td style="text-align:right;"> 24 </td>
   <td style="text-align:right;"> 93 </td>
   <td style="text-align:right;"> 90 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> 11.6% </td>
   <td style="text-align:left;"> 18.4% </td>
   <td style="text-align:right;"> 0.220 </td>
   <td style="text-align:right;"> 0.345 </td>
   <td style="text-align:right;"> 0.302 </td>
   <td style="text-align:right;"> 0.390 </td>
   <td style="text-align:right;"> 0.522 </td>
   <td style="text-align:right;"> 0.385 </td>
   <td style="text-align:right;"> 0.385 </td>
   <td style="text-align:right;"> 142 </td>
   <td style="text-align:right;"> 3.0 </td>
   <td style="text-align:right;"> 37.6 </td>
   <td style="text-align:right;"> -4.4 </td>
   <td style="text-align:right;"> 0.0 </td>
   <td style="text-align:right;"> 5.5 </td>
   <td style="text-align:right;"> 19326 </td>
  </tr>
</tbody>
<tfoot>
<tr><td style="padding: 0; " colspan="100%"><span style="font-style: italic;">Note: </span></td></tr>
<tr><td style="padding: 0; " colspan="100%">
<sup></sup> Source: FanGraphs</td></tr>
</tfoot>
</table>



