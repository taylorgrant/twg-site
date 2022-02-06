---
title: Tables
author: twg
date: '2022-02-05'
slug: tables
categories:
  - tables
  - DT
  - gt
  - kableExtra
tags:
  - tables
  - DT
  - gt
  - kableExtra
subtitle: ''
summary: 'Building clean tables with a few different packages'
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

Building tables with three different packages - [kableExtra](https://cran.r-project.org/web/packages/kableExtra/vignettes/awesome_table_in_html.html), [DT](https://rstudio.github.io/DT/), and [gt](https://gt.rstudio.com/). These builds aren’t meant to be extravagant, but are baselines for html and static tables.

## kableExtra

Starting with knitr and kableExtra to make html tables. They’re not interactive, but they look nice and require little code. Also, they don’t paginate, which is a pain. In this case, I’ve added links to each player’s wikipedia page using the `cell_spec()` function and then specifying `kbl("html", escape = FALSE)`.

Some watchouts:

-   Table Height - if you limit the height of the table, the horizontal scroll (if it’s necessary) is at the bottom of the table; it’s not persistent at the base of the table.
-   Justification - by default, numeric columns are right-justified. This data has two columns, BB% and K% that are strings and need to be justified. But, you can’t specify specific columns, it’s a vector of “l,” “c,” or “r.” Column headers follow the justification.

*The escape symbol ’\\' is showing up in the Salary column b/c some strange interference from DT and/or possibly widgetframe.*

``` r
mlb %>% 
  select(Name:AVG, `wRC+`, contains("WAR")) %>%
  select(-G19) %>%
  # formattable adds dollar sign and keeps it numeric
  mutate(Salary = formattable::currency(Salary, digits = 0L)) %>% 
  # setting up the URL, new_tab is FALSE by default
  mutate(Name = cell_spec(Name, "html", 
                          link = paste0("https://en.wikipedia.org/wiki/", 
                                        str_replace_all(Name, " ", "_")), 
                          new_tab = TRUE)) %>%
  rename(`2019` = WAR19, `2021` = WAR) %>%
  # table
  kbl("html", escape = FALSE,
    align = c("l","l",rep("r",15)), # justification; by default numeric or right-justified
    # format.args = list(big.mark = ",") # comma formatting if necessary
    ) %>% 
  # added styling with kableExtra
  kable_styling(bootstrap_options = c("striped", "hover", "responsive")) %>%
  add_header_above(c(" " = 15, "WAR" = 2)) %>%
  footnote(general = "Source: FanGraphs") %>%
  scroll_box(width = "100%", height = "600px")  # set height of table
```

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:600px; overflow-x: scroll; width:100%; ">

<table class="table table-striped table-hover table-responsive" style="margin-left: auto; margin-right: auto;border-bottom: 0;">
<thead>
<tr>
<th style="empty-cells: hide;border-bottom:hidden;position: sticky; top:0; background-color: #FFFFFF;" colspan="15">
</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; position: sticky; top:0; background-color: #FFFFFF;" colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

WAR

</div>

</th>
</tr>
<tr>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
Name
</th>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
Team
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
Salary
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
G
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
PA
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
HR
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
R
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
RBI
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
SB
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
BB%
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
K%
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
ISO
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
BABIP
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
AVG
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
wRC+
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
2019
</th>
<th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;">
2021
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Trea_Turner" style="     " target="_blank">Trea Turner</a>
</td>
<td style="text-align:left;">
—
</td>
<td style="text-align:right;">
\$13,000,000
</td>
<td style="text-align:right;">
148
</td>
<td style="text-align:right;">
646
</td>
<td style="text-align:right;">
28
</td>
<td style="text-align:right;">
107
</td>
<td style="text-align:right;">
77
</td>
<td style="text-align:right;">
32
</td>
<td style="text-align:right;">
6.3%
</td>
<td style="text-align:right;">
17.0%
</td>
<td style="text-align:right;">
0.208
</td>
<td style="text-align:right;">
0.362
</td>
<td style="text-align:right;">
0.328
</td>
<td style="text-align:right;">
142
</td>
<td style="text-align:right;">
3.6
</td>
<td style="text-align:right;">
6.9
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Vladimir_Guerrero_Jr." style="     " target="_blank">Vladimir Guerrero Jr.</a>
</td>
<td style="text-align:left;">
TOR
</td>
<td style="text-align:right;">
\$605,400
</td>
<td style="text-align:right;">
161
</td>
<td style="text-align:right;">
698
</td>
<td style="text-align:right;">
48
</td>
<td style="text-align:right;">
123
</td>
<td style="text-align:right;">
111
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
12.3%
</td>
<td style="text-align:right;">
15.8%
</td>
<td style="text-align:right;">
0.290
</td>
<td style="text-align:right;">
0.313
</td>
<td style="text-align:right;">
0.311
</td>
<td style="text-align:right;">
166
</td>
<td style="text-align:right;">
0.4
</td>
<td style="text-align:right;">
6.7
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Bryce_Harper" style="     " target="_blank">Bryce Harper</a>
</td>
<td style="text-align:left;">
PHI
</td>
<td style="text-align:right;">
\$27,538,462
</td>
<td style="text-align:right;">
141
</td>
<td style="text-align:right;">
599
</td>
<td style="text-align:right;">
35
</td>
<td style="text-align:right;">
101
</td>
<td style="text-align:right;">
84
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:right;">
16.7%
</td>
<td style="text-align:right;">
22.4%
</td>
<td style="text-align:right;">
0.305
</td>
<td style="text-align:right;">
0.359
</td>
<td style="text-align:right;">
0.309
</td>
<td style="text-align:right;">
170
</td>
<td style="text-align:right;">
4.5
</td>
<td style="text-align:right;">
6.6
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Marcus_Semien" style="     " target="_blank">Marcus Semien</a>
</td>
<td style="text-align:left;">
TOR
</td>
<td style="text-align:right;">
\$18,000,000
</td>
<td style="text-align:right;">
162
</td>
<td style="text-align:right;">
724
</td>
<td style="text-align:right;">
45
</td>
<td style="text-align:right;">
115
</td>
<td style="text-align:right;">
102
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:right;">
9.1%
</td>
<td style="text-align:right;">
20.2%
</td>
<td style="text-align:right;">
0.273
</td>
<td style="text-align:right;">
0.276
</td>
<td style="text-align:right;">
0.265
</td>
<td style="text-align:right;">
131
</td>
<td style="text-align:right;">
7.6
</td>
<td style="text-align:right;">
6.6
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Juan_Soto" style="     " target="_blank">Juan Soto</a>
</td>
<td style="text-align:left;">
WSN
</td>
<td style="text-align:right;">
\$8,500,000
</td>
<td style="text-align:right;">
151
</td>
<td style="text-align:right;">
654
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:right;">
111
</td>
<td style="text-align:right;">
95
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
22.2%
</td>
<td style="text-align:right;">
14.2%
</td>
<td style="text-align:right;">
0.221
</td>
<td style="text-align:right;">
0.332
</td>
<td style="text-align:right;">
0.313
</td>
<td style="text-align:right;">
163
</td>
<td style="text-align:right;">
4.9
</td>
<td style="text-align:right;">
6.6
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Jose_Ramirez" style="     " target="_blank">Jose Ramirez</a>
</td>
<td style="text-align:left;">
CLE
</td>
<td style="text-align:right;">
\$9,400,000
</td>
<td style="text-align:right;">
152
</td>
<td style="text-align:right;">
636
</td>
<td style="text-align:right;">
36
</td>
<td style="text-align:right;">
111
</td>
<td style="text-align:right;">
103
</td>
<td style="text-align:right;">
27
</td>
<td style="text-align:right;">
11.3%
</td>
<td style="text-align:right;">
13.7%
</td>
<td style="text-align:right;">
0.272
</td>
<td style="text-align:right;">
0.256
</td>
<td style="text-align:right;">
0.266
</td>
<td style="text-align:right;">
137
</td>
<td style="text-align:right;">
3.6
</td>
<td style="text-align:right;">
6.3
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Fernando_Tatis_Jr." style="     " target="_blank">Fernando Tatis Jr.</a>
</td>
<td style="text-align:left;">
SDP
</td>
<td style="text-align:right;">
\$1,714,286
</td>
<td style="text-align:right;">
130
</td>
<td style="text-align:right;">
546
</td>
<td style="text-align:right;">
42
</td>
<td style="text-align:right;">
99
</td>
<td style="text-align:right;">
97
</td>
<td style="text-align:right;">
25
</td>
<td style="text-align:right;">
11.4%
</td>
<td style="text-align:right;">
28.0%
</td>
<td style="text-align:right;">
0.328
</td>
<td style="text-align:right;">
0.324
</td>
<td style="text-align:right;">
0.282
</td>
<td style="text-align:right;">
156
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
6.1
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Carlos_Correa" style="     " target="_blank">Carlos Correa</a>
</td>
<td style="text-align:left;">
HOU
</td>
<td style="text-align:right;">
\$11,700,000
</td>
<td style="text-align:right;">
148
</td>
<td style="text-align:right;">
640
</td>
<td style="text-align:right;">
26
</td>
<td style="text-align:right;">
104
</td>
<td style="text-align:right;">
92
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
11.7%
</td>
<td style="text-align:right;">
18.1%
</td>
<td style="text-align:right;">
0.205
</td>
<td style="text-align:right;">
0.308
</td>
<td style="text-align:right;">
0.279
</td>
<td style="text-align:right;">
134
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
5.8
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Starling_Marte" style="     " target="_blank">Starling Marte</a>
</td>
<td style="text-align:left;">
—
</td>
<td style="text-align:right;">
\$12,500,000
</td>
<td style="text-align:right;">
120
</td>
<td style="text-align:right;">
526
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:right;">
89
</td>
<td style="text-align:right;">
55
</td>
<td style="text-align:right;">
47
</td>
<td style="text-align:right;">
8.2%
</td>
<td style="text-align:right;">
18.8%
</td>
<td style="text-align:right;">
0.148
</td>
<td style="text-align:right;">
0.372
</td>
<td style="text-align:right;">
0.310
</td>
<td style="text-align:right;">
134
</td>
<td style="text-align:right;">
3.0
</td>
<td style="text-align:right;">
5.5
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Aaron_Judge" style="     " target="_blank">Aaron Judge</a>
</td>
<td style="text-align:left;">
NYY
</td>
<td style="text-align:right;">
\$10,175,000
</td>
<td style="text-align:right;">
148
</td>
<td style="text-align:right;">
633
</td>
<td style="text-align:right;">
39
</td>
<td style="text-align:right;">
89
</td>
<td style="text-align:right;">
98
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
11.8%
</td>
<td style="text-align:right;">
25.0%
</td>
<td style="text-align:right;">
0.256
</td>
<td style="text-align:right;">
0.332
</td>
<td style="text-align:right;">
0.287
</td>
<td style="text-align:right;">
148
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
5.5
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Bryan_Reynolds" style="     " target="_blank">Bryan Reynolds</a>
</td>
<td style="text-align:left;">
PIT
</td>
<td style="text-align:right;">
\$601,000
</td>
<td style="text-align:right;">
159
</td>
<td style="text-align:right;">
646
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:right;">
93
</td>
<td style="text-align:right;">
90
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
11.6%
</td>
<td style="text-align:right;">
18.4%
</td>
<td style="text-align:right;">
0.220
</td>
<td style="text-align:right;">
0.345
</td>
<td style="text-align:right;">
0.302
</td>
<td style="text-align:right;">
142
</td>
<td style="text-align:right;">
3.2
</td>
<td style="text-align:right;">
5.5
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Brandon_Crawford" style="     " target="_blank">Brandon Crawford</a>
</td>
<td style="text-align:left;">
SFG
</td>
<td style="text-align:right;">
\$15,200,000
</td>
<td style="text-align:right;">
138
</td>
<td style="text-align:right;">
549
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:right;">
79
</td>
<td style="text-align:right;">
90
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:right;">
10.2%
</td>
<td style="text-align:right;">
19.1%
</td>
<td style="text-align:right;">
0.224
</td>
<td style="text-align:right;">
0.334
</td>
<td style="text-align:right;">
0.298
</td>
<td style="text-align:right;">
139
</td>
<td style="text-align:right;">
0.3
</td>
<td style="text-align:right;">
5.5
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Tyler_O'Neill" style="     " target="_blank">Tyler O’Neill</a>
</td>
<td style="text-align:left;">
STL
</td>
<td style="text-align:right;">
\$594,700
</td>
<td style="text-align:right;">
138
</td>
<td style="text-align:right;">
537
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:right;">
89
</td>
<td style="text-align:right;">
80
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:right;">
7.1%
</td>
<td style="text-align:right;">
31.3%
</td>
<td style="text-align:right;">
0.274
</td>
<td style="text-align:right;">
0.366
</td>
<td style="text-align:right;">
0.286
</td>
<td style="text-align:right;">
145
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
5.4
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Cedric_Mullins_II" style="     " target="_blank">Cedric Mullins II</a>
</td>
<td style="text-align:left;">
BAL
</td>
<td style="text-align:right;">
\$577,000
</td>
<td style="text-align:right;">
159
</td>
<td style="text-align:right;">
675
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:right;">
91
</td>
<td style="text-align:right;">
59
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:right;">
8.7%
</td>
<td style="text-align:right;">
18.5%
</td>
<td style="text-align:right;">
0.228
</td>
<td style="text-align:right;">
0.322
</td>
<td style="text-align:right;">
0.291
</td>
<td style="text-align:right;">
136
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
5.3
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Brandon_Lowe" style="     " target="_blank">Brandon Lowe</a>
</td>
<td style="text-align:left;">
TBR
</td>
<td style="text-align:right;">
\$2,500,000
</td>
<td style="text-align:right;">
149
</td>
<td style="text-align:right;">
615
</td>
<td style="text-align:right;">
39
</td>
<td style="text-align:right;">
97
</td>
<td style="text-align:right;">
99
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:right;">
11.1%
</td>
<td style="text-align:right;">
27.2%
</td>
<td style="text-align:right;">
0.277
</td>
<td style="text-align:right;">
0.280
</td>
<td style="text-align:right;">
0.247
</td>
<td style="text-align:right;">
137
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
5.2
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Xander_Bogaerts" style="     " target="_blank">Xander Bogaerts</a>
</td>
<td style="text-align:left;">
BOS
</td>
<td style="text-align:right;">
\$20,000,000
</td>
<td style="text-align:right;">
144
</td>
<td style="text-align:right;">
603
</td>
<td style="text-align:right;">
23
</td>
<td style="text-align:right;">
90
</td>
<td style="text-align:right;">
79
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
10.3%
</td>
<td style="text-align:right;">
18.7%
</td>
<td style="text-align:right;">
0.198
</td>
<td style="text-align:right;">
0.333
</td>
<td style="text-align:right;">
0.295
</td>
<td style="text-align:right;">
130
</td>
<td style="text-align:right;">
6.8
</td>
<td style="text-align:right;">
5.2
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Jose_Altuve" style="     " target="_blank">Jose Altuve</a>
</td>
<td style="text-align:left;">
HOU
</td>
<td style="text-align:right;">
\$29,000,000
</td>
<td style="text-align:right;">
146
</td>
<td style="text-align:right;">
678
</td>
<td style="text-align:right;">
31
</td>
<td style="text-align:right;">
117
</td>
<td style="text-align:right;">
83
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
9.7%
</td>
<td style="text-align:right;">
13.4%
</td>
<td style="text-align:right;">
0.211
</td>
<td style="text-align:right;">
0.280
</td>
<td style="text-align:right;">
0.278
</td>
<td style="text-align:right;">
130
</td>
<td style="text-align:right;">
3.5
</td>
<td style="text-align:right;">
5.2
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Shohei_Ohtani" style="     " target="_blank">Shohei Ohtani</a>
</td>
<td style="text-align:left;">
LAA
</td>
<td style="text-align:right;">
\$3,000,000
</td>
<td style="text-align:right;">
158
</td>
<td style="text-align:right;">
639
</td>
<td style="text-align:right;">
46
</td>
<td style="text-align:right;">
103
</td>
<td style="text-align:right;">
100
</td>
<td style="text-align:right;">
26
</td>
<td style="text-align:right;">
15.0%
</td>
<td style="text-align:right;">
29.6%
</td>
<td style="text-align:right;">
0.335
</td>
<td style="text-align:right;">
0.303
</td>
<td style="text-align:right;">
0.257
</td>
<td style="text-align:right;">
152
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
5.1
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Matt_Olson" style="     " target="_blank">Matt Olson</a>
</td>
<td style="text-align:left;">
OAK
</td>
<td style="text-align:right;">
\$5,000,000
</td>
<td style="text-align:right;">
156
</td>
<td style="text-align:right;">
673
</td>
<td style="text-align:right;">
39
</td>
<td style="text-align:right;">
101
</td>
<td style="text-align:right;">
111
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
13.1%
</td>
<td style="text-align:right;">
16.8%
</td>
<td style="text-align:right;">
0.269
</td>
<td style="text-align:right;">
0.269
</td>
<td style="text-align:right;">
0.271
</td>
<td style="text-align:right;">
146
</td>
<td style="text-align:right;">
3.9
</td>
<td style="text-align:right;">
5.0
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Paul_Goldschmidt" style="     " target="_blank">Paul Goldschmidt</a>
</td>
<td style="text-align:left;">
STL
</td>
<td style="text-align:right;">
\$25,333,333
</td>
<td style="text-align:right;">
158
</td>
<td style="text-align:right;">
679
</td>
<td style="text-align:right;">
31
</td>
<td style="text-align:right;">
102
</td>
<td style="text-align:right;">
99
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:right;">
9.9%
</td>
<td style="text-align:right;">
20.0%
</td>
<td style="text-align:right;">
0.221
</td>
<td style="text-align:right;">
0.331
</td>
<td style="text-align:right;">
0.294
</td>
<td style="text-align:right;">
138
</td>
<td style="text-align:right;">
3.0
</td>
<td style="text-align:right;">
4.9
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Bo_Bichette" style="     " target="_blank">Bo Bichette</a>
</td>
<td style="text-align:left;">
TOR
</td>
<td style="text-align:right;">
\$587,800
</td>
<td style="text-align:right;">
159
</td>
<td style="text-align:right;">
690
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:right;">
121
</td>
<td style="text-align:right;">
102
</td>
<td style="text-align:right;">
25
</td>
<td style="text-align:right;">
5.8%
</td>
<td style="text-align:right;">
19.9%
</td>
<td style="text-align:right;">
0.186
</td>
<td style="text-align:right;">
0.339
</td>
<td style="text-align:right;">
0.298
</td>
<td style="text-align:right;">
122
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
4.9
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Max_Muncy" style="     " target="_blank">Max Muncy</a>
</td>
<td style="text-align:left;">
LAD
</td>
<td style="text-align:right;">
\$9,000,000
</td>
<td style="text-align:right;">
144
</td>
<td style="text-align:right;">
592
</td>
<td style="text-align:right;">
36
</td>
<td style="text-align:right;">
95
</td>
<td style="text-align:right;">
94
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
14.0%
</td>
<td style="text-align:right;">
20.3%
</td>
<td style="text-align:right;">
0.278
</td>
<td style="text-align:right;">
0.257
</td>
<td style="text-align:right;">
0.249
</td>
<td style="text-align:right;">
140
</td>
<td style="text-align:right;">
4.7
</td>
<td style="text-align:right;">
4.9
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Kyle_Tucker" style="     " target="_blank">Kyle Tucker</a>
</td>
<td style="text-align:left;">
HOU
</td>
<td style="text-align:right;">
\$624,300
</td>
<td style="text-align:right;">
140
</td>
<td style="text-align:right;">
567
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:right;">
83
</td>
<td style="text-align:right;">
92
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:right;">
9.3%
</td>
<td style="text-align:right;">
15.9%
</td>
<td style="text-align:right;">
0.263
</td>
<td style="text-align:right;">
0.304
</td>
<td style="text-align:right;">
0.294
</td>
<td style="text-align:right;">
147
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
4.8
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Rafael_Devers" style="     " target="_blank">Rafael Devers</a>
</td>
<td style="text-align:left;">
BOS
</td>
<td style="text-align:right;">
\$4,575,000
</td>
<td style="text-align:right;">
156
</td>
<td style="text-align:right;">
664
</td>
<td style="text-align:right;">
38
</td>
<td style="text-align:right;">
101
</td>
<td style="text-align:right;">
113
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
9.3%
</td>
<td style="text-align:right;">
21.5%
</td>
<td style="text-align:right;">
0.259
</td>
<td style="text-align:right;">
0.307
</td>
<td style="text-align:right;">
0.279
</td>
<td style="text-align:right;">
134
</td>
<td style="text-align:right;">
5.9
</td>
<td style="text-align:right;">
4.7
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Yoan_Moncada" style="     " target="_blank">Yoan Moncada</a>
</td>
<td style="text-align:left;">
CHW
</td>
<td style="text-align:right;">
\$6,800,000
</td>
<td style="text-align:right;">
144
</td>
<td style="text-align:right;">
616
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:right;">
74
</td>
<td style="text-align:right;">
61
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
13.6%
</td>
<td style="text-align:right;">
25.5%
</td>
<td style="text-align:right;">
0.148
</td>
<td style="text-align:right;">
0.350
</td>
<td style="text-align:right;">
0.263
</td>
<td style="text-align:right;">
122
</td>
<td style="text-align:right;">
5.6
</td>
<td style="text-align:right;">
4.5
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Freddie_Freeman" style="     " target="_blank">Freddie Freeman</a>
</td>
<td style="text-align:left;">
ATL
</td>
<td style="text-align:right;">
\$22,359,375
</td>
<td style="text-align:right;">
159
</td>
<td style="text-align:right;">
695
</td>
<td style="text-align:right;">
31
</td>
<td style="text-align:right;">
120
</td>
<td style="text-align:right;">
83
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:right;">
12.2%
</td>
<td style="text-align:right;">
15.4%
</td>
<td style="text-align:right;">
0.203
</td>
<td style="text-align:right;">
0.321
</td>
<td style="text-align:right;">
0.300
</td>
<td style="text-align:right;">
135
</td>
<td style="text-align:right;">
3.9
</td>
<td style="text-align:right;">
4.5
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/J.T._Realmuto" style="     " target="_blank">J.T. Realmuto</a>
</td>
<td style="text-align:left;">
PHI
</td>
<td style="text-align:right;">
\$18,048,556
</td>
<td style="text-align:right;">
134
</td>
<td style="text-align:right;">
537
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:right;">
64
</td>
<td style="text-align:right;">
73
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:right;">
8.9%
</td>
<td style="text-align:right;">
24.0%
</td>
<td style="text-align:right;">
0.176
</td>
<td style="text-align:right;">
0.325
</td>
<td style="text-align:right;">
0.263
</td>
<td style="text-align:right;">
108
</td>
<td style="text-align:right;">
5.7
</td>
<td style="text-align:right;">
4.4
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Jake_Cronenworth" style="     " target="_blank">Jake Cronenworth</a>
</td>
<td style="text-align:left;">
SDP
</td>
<td style="text-align:right;">
\$584,900
</td>
<td style="text-align:right;">
152
</td>
<td style="text-align:right;">
643
</td>
<td style="text-align:right;">
21
</td>
<td style="text-align:right;">
94
</td>
<td style="text-align:right;">
71
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
8.6%
</td>
<td style="text-align:right;">
14.0%
</td>
<td style="text-align:right;">
0.194
</td>
<td style="text-align:right;">
0.283
</td>
<td style="text-align:right;">
0.266
</td>
<td style="text-align:right;">
116
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
4.4
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Manny_Machado" style="     " target="_blank">Manny Machado</a>
</td>
<td style="text-align:left;">
SDP
</td>
<td style="text-align:right;">
\$34,000,000
</td>
<td style="text-align:right;">
153
</td>
<td style="text-align:right;">
640
</td>
<td style="text-align:right;">
28
</td>
<td style="text-align:right;">
92
</td>
<td style="text-align:right;">
106
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:right;">
9.8%
</td>
<td style="text-align:right;">
15.9%
</td>
<td style="text-align:right;">
0.211
</td>
<td style="text-align:right;">
0.290
</td>
<td style="text-align:right;">
0.278
</td>
<td style="text-align:right;">
122
</td>
<td style="text-align:right;">
3.2
</td>
<td style="text-align:right;">
4.4
</td>
</tr>
<tr>
<td style="text-align:left;">
<a href="https://en.wikipedia.org/wiki/Nicky_Lopez" style="     " target="_blank">Nicky Lopez</a>
</td>
<td style="text-align:left;">
KCR
</td>
<td style="text-align:right;">
\$597,500
</td>
<td style="text-align:right;">
151
</td>
<td style="text-align:right;">
565
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
78
</td>
<td style="text-align:right;">
43
</td>
<td style="text-align:right;">
22
</td>
<td style="text-align:right;">
8.7%
</td>
<td style="text-align:right;">
13.1%
</td>
<td style="text-align:right;">
0.078
</td>
<td style="text-align:right;">
0.347
</td>
<td style="text-align:right;">
0.300
</td>
<td style="text-align:right;">
106
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:right;">
4.4
</td>
</tr>
</tbody>
<tfoot>
<tr>
<td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span>
</td>
</tr>
<tr>
<td style="padding: 0; " colspan="100%">
<sup></sup> Source: FanGraphs
</td>
</tr>
</tfoot>
</table>

</div>

## DT Datable

DT is another html table, but it’s far more interactive. Columns are sortable and filterable by default. We’re going to convert the string columns to numeric so that we can use the DT format functions.

The DT table won’t render, but this code will work in R or in any HTML Rmarkdown doc.

``` r
mlb %>% 
  select(Name:AVG, `wRC+`, contains("WAR")) %>%
  select(-G19) %>%
  mutate(across(contains("%"), ~as.numeric(str_replace_all(., "%", ""))/100)) %>%
  # create a link to player's wiki 
  mutate(
    link = paste0("https://en.wikipedia.org/wiki/", 
                                        str_replace_all(Name, " ", "_")),
    Name = paste0('<a href="',link,'"target="_blank">',Name,"</a>")) %>%
  DT::datatable(
    fillContainer = TRUE, # make it scrollable
    escape = FALSE, # for links to work
    caption = "DT Version - MLB Table",
    class = "display",
    filter = 'top',
    rownames = FALSE,
    extensions = "Buttons", 
    options = list(
      dom = "Bfrtip",
      buttons = list( # download data
        list(
        extend = "collection",
        buttons = c("csv", "excel", "pdf"),
        text = "Download"
        )),
      pageLength = 20, # how many rows per page
      columnDefs = list(
        list(
          visible = FALSE, targets = 17) # hiding a column
        ))
    ) %>% 
  # formatting numbers
  formatPercentage(c(10,11), digits = 1) %>% 
  formatCurrency(columns = "Salary", currency = "$", mark = ",", digits = 0)
```

## gt Tables

Last, gt tables, which are static but very customizable, and they are great for presentations and decks. also, because gt allows you rename columns in the table, we are allowed to have multiple columns with the same name, which generally isn’t allowed in other tables.

It’s also very easy to add imagery and symbols into the tables.

``` r
# arrows for fun
up_arrow <- "<span style=\"color:green\">&#9650;</span>"
down_arrow <- "<span style=\"color:red\">&#9660;</span>"

mlb %>% 
  select(Name:AVG, `wRC+`, contains("WAR")) %>% 
  mutate(across(contains("%"), ~as.numeric(str_replace_all(., "%", ""))/100)) %>% 
  # set up table
  gt(rowname_col = "Name") %>% 
  # title and subtitle
  tab_header( 
    title = md("**gt Table: MLB Table**"),
    subtitle = "Top 30 by fWAR"
  ) %>%
  # caption 
  tab_source_note( # adding source note
    source_note = md("*Source: FanGraphs*") 
  ) %>% 
  # create rowname groupings 
  tab_row_group(
    label = md("Mega Salary (Over $10MM)"),
    rows = Salary > 10e6
  ) %>% 
  tab_row_group(
    label = "Market-Value (3-$10MM)",
    rows = (Salary > 3e6 & Salary <= 10e6)
  ) %>% 
  tab_row_group(
    label = "Great Deal (1-$3MM)",
    row = (Salary >= 1e6 & Salary <= 3e6)
    ) %>% 
  tab_row_group(
    label = "Highway Robbery (< $1MM)",
    row = (Salary < 1e6)
    ) %>%
  # reorder groups 
  row_group_order(
    groups = c("Mega Salary (Over $10MM)", "Market-Value (3-$10MM)",
               "Great Deal (1-$3MM)", "Highway Robbery (< $1MM)")
  ) %>% 
  # add spanner over WAR
  tab_spanner(
    label = "WAR",
    columns = matches("WAR")
  ) %>% 
  # add spanner over WAR
  tab_spanner(
    label = "Games",
    columns = c("G19", "G")
  ) %>%
  # format columns
  fmt_currency(
    columns = "Salary",
    currency = "USD",
    sep_mark = ",",
    suffixing = TRUE, # cut down, e.g, $1M or $650K
    use_subunits = FALSE
    ) %>% 
  fmt_percent(
    columns = matches("BB%|K%"),
    decimals = 1
  ) %>% 
  # styling the table --------------------------- 
  opt_table_font(
    font = list(
      google_font("Open Sans")
    )
  ) %>%
  # aligning the title and subtitle left
  tab_style( 
    style = cell_text(align = 'left',
                      weight = 'bold',
                      size = px(18)),
    locations = cells_title(c("title"))
  ) %>%
  # alignment of spanner
  tab_style(
    style = cell_text(weight = "bold",
                      size = px(13),
                      align = "center"),
    locations = list(
      cells_column_spanners(matches("WAR|Games")))
  ) %>%
  # bolding groups and columns 
  tab_style(
    style = cell_text(weight = 'bold',
                      size = px(13)),
    locations = list(
      cells_column_labels(
        columns = everything()),
      cells_row_groups(groups = TRUE))
  ) %>%
  # align column headers 
  tab_style(
    style = cell_text(align = "center"),
    locations = list(
      cells_column_labels(
        columns = everything()
      ))
  ) %>%
  # indenting the stub (more room for the identifiers)
  tab_style( 
    style = cell_text(indent = px(8),
                      size = px(12)),
    locations = cells_stub()
  ) %>%
   # font size in table
  tab_style(
    style = cell_text(size = px(12)),
    locations = cells_body(
      columns = everything())
  ) %>%
   # font alignment
  tab_style(
    style = cell_text(align = "center"),
    locations = cells_body(
      columns = !contains("Name"))
  ) %>% 
  # adding red/green arrows based on WAR
  text_transform(
    locations = list(
      cells_body(columns = "WAR",
                 rows = WAR > WAR19)
    ),
    fn = function(x) paste(x, up_arrow)
  ) %>%
  text_transform(
    locations = list(
      cells_body(columns = "WAR",
                 rows = WAR < WAR19)
    ),
    fn = function(x) paste(x, down_arrow)
  ) %>%
  # rename columns if necessary
  cols_label(
    G19 = "2019", G = "2021",
    WAR19 = "2019", WAR = "2021"
  ) %>%
  # hide columne for space
  cols_hide(
    columns = 'ISO'
  ) %>% 
  # final options
  tab_options(
    table.background.color = "#F3F3F3",
    data_row.padding = px(6),
    row_group.padding = px(6),
    source_notes.font.size = px(10),
    footnotes.font.size = px(10),
    footnotes.marks = "LETTERS",
    table.font.names = "Open Sans"
  )
```

<div id="swmlvxnacn" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>@import url("https://fonts.googleapis.com/css2?family=Open+Sans:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
html {
  font-family: 'Open Sans';
}

#swmlvxnacn .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #F3F3F3;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#swmlvxnacn .gt_heading {
  background-color: #F3F3F3;
  text-align: center;
  border-bottom-color: #F3F3F3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#swmlvxnacn .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  border-bottom-color: #F3F3F3;
  border-bottom-width: 0;
}

#swmlvxnacn .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  border-top-color: #F3F3F3;
  border-top-width: 0;
}

#swmlvxnacn .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#swmlvxnacn .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#swmlvxnacn .gt_col_heading {
  color: #333333;
  background-color: #F3F3F3;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#swmlvxnacn .gt_column_spanner_outer {
  color: #333333;
  background-color: #F3F3F3;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#swmlvxnacn .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#swmlvxnacn .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#swmlvxnacn .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#swmlvxnacn .gt_group_heading {
  padding: 6px;
  color: #333333;
  background-color: #F3F3F3;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#swmlvxnacn .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #F3F3F3;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#swmlvxnacn .gt_from_md > :first-child {
  margin-top: 0;
}

#swmlvxnacn .gt_from_md > :last-child {
  margin-bottom: 0;
}

#swmlvxnacn .gt_row {
  padding-top: 6px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#swmlvxnacn .gt_stub {
  color: #333333;
  background-color: #F3F3F3;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 12px;
}

#swmlvxnacn .gt_summary_row {
  color: #333333;
  background-color: #F3F3F3;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#swmlvxnacn .gt_first_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
}

#swmlvxnacn .gt_grand_summary_row {
  color: #333333;
  background-color: #F3F3F3;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#swmlvxnacn .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#swmlvxnacn .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#swmlvxnacn .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#swmlvxnacn .gt_footnotes {
  color: #333333;
  background-color: #F3F3F3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#swmlvxnacn .gt_footnote {
  margin: 0px;
  font-size: 10px;
  padding: 4px;
}

#swmlvxnacn .gt_sourcenotes {
  color: #333333;
  background-color: #F3F3F3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#swmlvxnacn .gt_sourcenote {
  font-size: 10px;
  padding: 4px;
}

#swmlvxnacn .gt_left {
  text-align: left;
}

#swmlvxnacn .gt_center {
  text-align: center;
}

#swmlvxnacn .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#swmlvxnacn .gt_font_normal {
  font-weight: normal;
}

#swmlvxnacn .gt_font_bold {
  font-weight: bold;
}

#swmlvxnacn .gt_font_italic {
  font-style: italic;
}

#swmlvxnacn .gt_super {
  font-size: 65%;
}

#swmlvxnacn .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 65%;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="17" class="gt_heading gt_title gt_font_normal" style="font-size: 18px; text-align: left; font-weight: bold;"><strong>gt Table: MLB Table</strong></th>
    </tr>
    <tr>
      <th colspan="17" class="gt_heading gt_subtitle gt_font_normal gt_bottom_border" style>Top 30 by fWAR</th>
    </tr>
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1"></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">Team</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">Salary</th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2" style="font-size: 13px; text-align: center; font-weight: bold;">
        <span class="gt_column_spanner">Games</span>
      </th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">PA</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">HR</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">R</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">RBI</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">SB</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">BB%</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">K%</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">BABIP</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">AVG</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">wRC+</th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2" style="font-size: 13px; text-align: center; font-weight: bold;">
        <span class="gt_column_spanner">WAR</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">2019</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">2021</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">2019</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;">2021</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr class="gt_group_heading_row">
      <td colspan="17" class="gt_group_heading" style="font-size: 13px; font-weight: bold;">Mega Salary (Over $10MM)</td>
    </tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Trea Turner</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">---</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$13M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">122</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">148</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">646</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">28</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">107</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">77</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">32</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.3&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">17.0&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.362</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.328</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">142</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.6</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.9 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Bryce Harper</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">PHI</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$28M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">157</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">141</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">599</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">35</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">101</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">84</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">13</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">16.7&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">22.4&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.359</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.309</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">170</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.5</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.6 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Marcus Semien</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">TOR</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$18M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">162</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">162</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">724</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">45</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">115</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">102</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">15</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.1&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">20.2&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.276</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.265</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">131</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">7.6</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.6 <span style="color:red">&#9660;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Carlos Correa</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">HOU</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$12M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">148</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">640</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">26</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">104</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">92</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.7&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">18.1&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.308</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.279</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">134</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.8</td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Starling Marte</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">---</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$12M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">132</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">120</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">526</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">12</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">89</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">55</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">47</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">8.2&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">18.8&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.372</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.310</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">134</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.0</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.5 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Aaron Judge</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">NYY</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$10M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">148</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">633</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">39</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">89</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">98</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">6</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.8&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">25.0&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.332</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.287</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">148</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.5</td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Brandon Crawford</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">SFG</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$15M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">147</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">138</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">549</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">24</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">79</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">90</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">11</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">10.2&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">19.1&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.334</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.298</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">139</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.3</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.5 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Xander Bogaerts</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">BOS</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$20M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">155</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">144</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">603</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">23</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">90</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">79</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">10.3&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">18.7&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.333</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.295</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">130</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.8</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.2 <span style="color:red">&#9660;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Jose Altuve</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">HOU</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$29M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">124</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">146</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">678</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">31</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">117</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">83</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.7&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">13.4&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.280</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.278</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">130</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.5</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.2 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Paul Goldschmidt</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">STL</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$25M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">161</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">158</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">679</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">31</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">102</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">99</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">12</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.9&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">20.0&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.331</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.294</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">138</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.0</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.9 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Freddie Freeman</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">ATL</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$22M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">158</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">159</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">695</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">31</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">120</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">83</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">8</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">12.2&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">15.4&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.321</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.300</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">135</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.9</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.5 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">J.T. Realmuto</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">PHI</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$18M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">145</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">134</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">537</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">17</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">64</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">73</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">13</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">8.9&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">24.0&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.325</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.263</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">108</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.7</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.4 <span style="color:red">&#9660;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Manny Machado</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">SDP</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$34M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">156</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">153</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">640</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">28</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">92</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">106</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">12</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.8&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">15.9&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.290</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.278</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">122</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.2</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.4 <span style="color:green">&#9650;</span></td></tr>
    <tr class="gt_group_heading_row">
      <td colspan="17" class="gt_group_heading" style="font-size: 13px; font-weight: bold;">Market-Value (3-$10MM)</td>
    </tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Juan Soto</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">WSN</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$8M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">150</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">151</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">654</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">29</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">111</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">95</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">9</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">22.2&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">14.2&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.332</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.313</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">163</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.9</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.6 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Jose Ramirez</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">CLE</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$9M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">129</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">152</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">636</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">36</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">111</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">103</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">27</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.3&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">13.7&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.256</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.266</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">137</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.6</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.3 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Matt Olson</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">OAK</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$5M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">127</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">156</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">673</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">39</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">101</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">111</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">13.1&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">16.8&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.269</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.271</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">146</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.9</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.0 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Max Muncy</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">LAD</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$9M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">141</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">144</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">592</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">36</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">95</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">94</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">2</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">14.0&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">20.3&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.257</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.249</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">140</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.7</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.9 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Rafael Devers</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">BOS</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$5M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">156</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">156</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">664</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">38</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">101</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">113</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.3&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">21.5&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.307</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.279</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">134</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.9</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.7 <span style="color:red">&#9660;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Yoan Moncada</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">CHW</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$7M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">132</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">144</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">616</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">14</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">74</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">61</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">3</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">13.6&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">25.5&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.350</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.263</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">122</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.6</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.5 <span style="color:red">&#9660;</span></td></tr>
    <tr class="gt_group_heading_row">
      <td colspan="17" class="gt_group_heading" style="font-size: 13px; font-weight: bold;">Great Deal (1-$3MM)</td>
    </tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Fernando Tatis Jr.</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">SDP</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$2M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">130</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">546</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">42</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">99</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">97</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">25</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.4&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">28.0&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.324</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.282</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">156</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.1</td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Brandon Lowe</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">TBR</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$2M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">149</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">615</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">39</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">97</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">99</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">7</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.1&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">27.2&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.280</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.247</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">137</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.2</td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Shohei Ohtani</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">LAA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$3M</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">158</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">639</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">46</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">103</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">100</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">26</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">15.0&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">29.6&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.303</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.257</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">152</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.1</td></tr>
    <tr class="gt_group_heading_row">
      <td colspan="17" class="gt_group_heading" style="font-size: 13px; font-weight: bold;">Highway Robbery (&lt; $1MM)</td>
    </tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Vladimir Guerrero Jr.</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">TOR</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$605K</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">123</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">161</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">698</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">48</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">123</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">111</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">12.3&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">15.8&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.313</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.311</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">166</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.4</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.7 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Bryan Reynolds</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">PIT</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$601K</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">134</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">159</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">646</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">24</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">93</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">90</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.6&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">18.4&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.345</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.302</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">142</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.2</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.5 <span style="color:green">&#9650;</span></td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Tyler O'Neill</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">STL</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$595K</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">138</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">537</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">34</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">89</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">80</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">15</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">7.1&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">31.3&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.366</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.286</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">145</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.4</td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Cedric Mullins II</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">BAL</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$577K</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">159</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">675</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">30</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">91</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">59</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">30</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">8.7&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">18.5&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.322</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.291</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">136</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.3</td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Bo Bichette</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">TOR</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$588K</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">159</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">690</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">29</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">121</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">102</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">25</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.8&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">19.9&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.339</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.298</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">122</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.9</td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Kyle Tucker</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">HOU</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$624K</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">140</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">567</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">30</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">83</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">92</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">14</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.3&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">15.9&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.304</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.294</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">147</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.8</td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Jake Cronenworth</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">SDP</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$585K</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">152</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">643</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">21</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">94</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">71</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">8.6&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">14.0&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.283</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.266</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">116</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.4</td></tr>
    <tr><td class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Nicky Lopez</td>
<td class="gt_row gt_left" style="font-size: 12px; text-align: center;">KCR</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">$598K</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">151</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">565</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">2</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">78</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">43</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">22</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">8.7&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">13.1&percnt;</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.347</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.300</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">106</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.4</td></tr>
  </tbody>
  <tfoot class="gt_sourcenotes">
    <tr>
      <td class="gt_sourcenote" colspan="17"><em>Source: FanGraphs</em></td>
    </tr>
  </tfoot>
  
</table>
</div>
