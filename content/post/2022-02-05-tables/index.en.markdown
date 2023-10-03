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
<script src="{{< blogdown/postref >}}index.en_files/htmlwidgets/htmlwidgets.js"></script>
<script src="{{< blogdown/postref >}}index.en_files/pymjs/pym.v1.js"></script>
<script src="{{< blogdown/postref >}}index.en_files/widgetframe-binding/widgetframe.js"></script>

Building tables with three different packages - [kableExtra](https://cran.r-project.org/web/packages/kableExtra/vignettes/awesome_table_in_html.html), [DT](https://rstudio.github.io/DT/), and [gt](https://gt.rstudio.com/). These builds aren’t meant to be extravagant, but are baselines for html and static tables.

## kableExtra

Starting with knitr and kableExtra to make html tables. They’re not interactive, but they look nice and require little code. Also, they don’t paginate, which is a pain. In this case, I’ve added links to each player’s wikipedia page using the `cell_spec()` function and then specifying `kbl("html", escape = FALSE)`.

Some watchouts:

- Table Height - if you limit the height of the table, the horizontal scroll (if it’s necessary) is at the bottom of the table; it’s not persistent at the base of the table.
- Justification - by default, numeric columns are right-justified. This data has two columns, BB% and K% that are strings and need to be justified. But, you can’t specify specific columns, it’s a vector of “l”, “c”, or “r”. Column headers follow the justification.

*The escape symbol ’\\ is showing up in the Salary column b/c some strange interference from DT and/or possibly widgetframe.*

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

The DT table will only render via the `widgetframe` package, but it works without it on any Rmarkdown or Shiny app.

``` r
library(widgetframe)
```

    ## Loading required package: htmlwidgets

``` r
dt <- mlb %>%
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

frameWidget(dt, height = 500, width = '95%') 
```

<div id="htmlwidget-1" style="width:95%;height:500px;" class="widgetframe html-widget "></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"url":"index.en_files/figure-html//widgets/widget_unnamed-chunk-2.html","options":{"xdomain":"*","allowfullscreen":false,"lazyload":false}},"evals":[],"jsHooks":[]}</script>

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
  rename(WAR21 = WAR, G21 = G) %>%
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
  # add spanner over G
  tab_spanner(
    label = "Games",
    columns = c("G19", "G21")
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
      cells_body(columns = c("WAR21"),
                 rows = WAR21 > WAR19)
    ),
    fn = function(x) paste(x, up_arrow)
  ) %>%
  text_transform(
    locations = list(
      cells_body(columns = c("WAR21"),
                 rows = WAR21 < WAR19)
    ),
    fn = function(x) paste(x, down_arrow)
  ) %>%
  # rename columns if necessary
  cols_label(
    G19 = "2019", G21 = "2021",
    WAR19 = "2019", WAR21 = "2021"
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

<div id="ggmagwwaui" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>@import url("https://fonts.googleapis.com/css2?family=Open+Sans:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
#ggmagwwaui table {
  font-family: 'Open Sans';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
&#10;#ggmagwwaui thead, #ggmagwwaui tbody, #ggmagwwaui tfoot, #ggmagwwaui tr, #ggmagwwaui td, #ggmagwwaui th {
  border-style: none;
}
&#10;#ggmagwwaui p {
  margin: 0;
  padding: 0;
}
&#10;#ggmagwwaui .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
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
&#10;#ggmagwwaui .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#ggmagwwaui .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #F3F3F3;
  border-bottom-width: 0;
}
&#10;#ggmagwwaui .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #F3F3F3;
  border-top-width: 0;
}
&#10;#ggmagwwaui .gt_heading {
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
&#10;#ggmagwwaui .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#ggmagwwaui .gt_col_headings {
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
&#10;#ggmagwwaui .gt_col_heading {
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
&#10;#ggmagwwaui .gt_column_spanner_outer {
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
&#10;#ggmagwwaui .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#ggmagwwaui .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#ggmagwwaui .gt_column_spanner {
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
&#10;#ggmagwwaui .gt_spanner_row {
  border-bottom-style: hidden;
}
&#10;#ggmagwwaui .gt_group_heading {
  padding-top: 6px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
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
  text-align: left;
}
&#10;#ggmagwwaui .gt_empty_group_heading {
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
&#10;#ggmagwwaui .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#ggmagwwaui .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#ggmagwwaui .gt_row {
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
&#10;#ggmagwwaui .gt_stub {
  color: #333333;
  background-color: #F3F3F3;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ggmagwwaui .gt_stub_row_group {
  color: #333333;
  background-color: #F3F3F3;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}
&#10;#ggmagwwaui .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#ggmagwwaui .gt_row_group_first th {
  border-top-width: 2px;
}
&#10;#ggmagwwaui .gt_summary_row {
  color: #333333;
  background-color: #F3F3F3;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ggmagwwaui .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#ggmagwwaui .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#ggmagwwaui .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#ggmagwwaui .gt_grand_summary_row {
  color: #333333;
  background-color: #F3F3F3;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ggmagwwaui .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#ggmagwwaui .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}
&#10;#ggmagwwaui .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#ggmagwwaui .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#ggmagwwaui .gt_footnotes {
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
&#10;#ggmagwwaui .gt_footnote {
  margin: 0px;
  font-size: 10px;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ggmagwwaui .gt_sourcenotes {
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
&#10;#ggmagwwaui .gt_sourcenote {
  font-size: 10px;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ggmagwwaui .gt_left {
  text-align: left;
}
&#10;#ggmagwwaui .gt_center {
  text-align: center;
}
&#10;#ggmagwwaui .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#ggmagwwaui .gt_font_normal {
  font-weight: normal;
}
&#10;#ggmagwwaui .gt_font_bold {
  font-weight: bold;
}
&#10;#ggmagwwaui .gt_font_italic {
  font-style: italic;
}
&#10;#ggmagwwaui .gt_super {
  font-size: 65%;
}
&#10;#ggmagwwaui .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}
&#10;#ggmagwwaui .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#ggmagwwaui .gt_indent_1 {
  text-indent: 5px;
}
&#10;#ggmagwwaui .gt_indent_2 {
  text-indent: 10px;
}
&#10;#ggmagwwaui .gt_indent_3 {
  text-indent: 15px;
}
&#10;#ggmagwwaui .gt_indent_4 {
  text-indent: 20px;
}
&#10;#ggmagwwaui .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_heading">
      <td colspan="17" class="gt_heading gt_title gt_font_normal" style="font-size: 18px; text-align: left; font-weight: bold;"><strong>gt Table: MLB Table</strong></td>
    </tr>
    <tr class="gt_heading">
      <td colspan="17" class="gt_heading gt_subtitle gt_font_normal gt_bottom_border" style>Top 30 by fWAR</td>
    </tr>
    <tr class="gt_col_headings gt_spanner_row">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1" scope="col" id=""></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="Team">Team</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="Salary">Salary</th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2" style="font-size: 13px; text-align: center; font-weight: bold;" scope="colgroup" id="Games">
        <span class="gt_column_spanner">Games</span>
      </th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="PA">PA</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="HR">HR</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="R">R</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="RBI">RBI</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="SB">SB</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="BB%">BB%</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="K%">K%</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="BABIP">BABIP</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="AVG">AVG</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="wRC+">wRC+</th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2" style="font-size: 13px; text-align: center; font-weight: bold;" scope="colgroup" id="WAR">
        <span class="gt_column_spanner">WAR</span>
      </th>
    </tr>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="2019">2019</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="2021">2021</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="2019">2019</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="2021">2021</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr class="gt_group_heading_row">
      <th colspan="17" class="gt_group_heading" style="font-size: 13px; font-weight: bold;" scope="colgroup" id="Mega Salary (Over $10MM)">Mega Salary (Over $10MM)</th>
    </tr>
    <tr class="gt_row_group_first"><th id="stub_1_1" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Trea Turner</th>
<td headers="Mega Salary (Over $10MM) stub_1_1 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">---</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$13M</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">122</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">148</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">646</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">28</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">107</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">77</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">32</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.3%</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">17.0%</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.362</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.328</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">142</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.6</td>
<td headers="Mega Salary (Over $10MM) stub_1_1 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.9 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_2" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Bryce Harper</th>
<td headers="Mega Salary (Over $10MM) stub_1_2 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">PHI</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$28M</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">157</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">141</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">599</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">35</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">101</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">84</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">13</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">16.7%</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">22.4%</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.359</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.309</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">170</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.5</td>
<td headers="Mega Salary (Over $10MM) stub_1_2 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.6 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_3" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Marcus Semien</th>
<td headers="Mega Salary (Over $10MM) stub_1_3 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">TOR</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$18M</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">162</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">162</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">724</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">45</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">115</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">102</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">15</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.1%</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">20.2%</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.276</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.265</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">131</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">7.6</td>
<td headers="Mega Salary (Over $10MM) stub_1_3 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.6 <span style="color:red">&#9660;</span></td></tr>
    <tr><th id="stub_1_4" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Carlos Correa</th>
<td headers="Mega Salary (Over $10MM) stub_1_4 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">HOU</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$12M</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">148</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">640</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">26</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">104</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">92</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.7%</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">18.1%</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.308</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.279</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">134</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Mega Salary (Over $10MM) stub_1_4 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.8</td></tr>
    <tr><th id="stub_1_5" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Starling Marte</th>
<td headers="Mega Salary (Over $10MM) stub_1_5 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">---</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$12M</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">132</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">120</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">526</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">12</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">89</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">55</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">47</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">8.2%</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">18.8%</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.372</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.310</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">134</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.0</td>
<td headers="Mega Salary (Over $10MM) stub_1_5 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.5 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_6" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Aaron Judge</th>
<td headers="Mega Salary (Over $10MM) stub_1_6 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">NYY</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$10M</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">148</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">633</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">39</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">89</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">98</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">6</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.8%</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">25.0%</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.332</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.287</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">148</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Mega Salary (Over $10MM) stub_1_6 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.5</td></tr>
    <tr><th id="stub_1_7" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Brandon Crawford</th>
<td headers="Mega Salary (Over $10MM) stub_1_7 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">SFG</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$15M</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">147</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">138</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">549</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">24</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">79</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">90</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">11</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">10.2%</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">19.1%</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.334</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.298</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">139</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.3</td>
<td headers="Mega Salary (Over $10MM) stub_1_7 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.5 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_8" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Xander Bogaerts</th>
<td headers="Mega Salary (Over $10MM) stub_1_8 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">BOS</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$20M</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">155</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">144</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">603</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">23</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">90</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">79</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">10.3%</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">18.7%</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.333</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.295</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">130</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.8</td>
<td headers="Mega Salary (Over $10MM) stub_1_8 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.2 <span style="color:red">&#9660;</span></td></tr>
    <tr><th id="stub_1_9" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Jose Altuve</th>
<td headers="Mega Salary (Over $10MM) stub_1_9 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">HOU</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$29M</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">124</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">146</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">678</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">31</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">117</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">83</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.7%</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">13.4%</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.280</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.278</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">130</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.5</td>
<td headers="Mega Salary (Over $10MM) stub_1_9 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.2 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_10" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Paul Goldschmidt</th>
<td headers="Mega Salary (Over $10MM) stub_1_10 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">STL</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$25M</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">161</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">158</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">679</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">31</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">102</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">99</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">12</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.9%</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">20.0%</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.331</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.294</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">138</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.0</td>
<td headers="Mega Salary (Over $10MM) stub_1_10 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.9 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_11" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Freddie Freeman</th>
<td headers="Mega Salary (Over $10MM) stub_1_11 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">ATL</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$22M</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">158</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">159</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">695</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">31</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">120</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">83</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">8</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">12.2%</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">15.4%</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.321</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.300</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">135</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.9</td>
<td headers="Mega Salary (Over $10MM) stub_1_11 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.5 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_12" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">J.T. Realmuto</th>
<td headers="Mega Salary (Over $10MM) stub_1_12 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">PHI</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$18M</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">145</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">134</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">537</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">17</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">64</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">73</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">13</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">8.9%</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">24.0%</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.325</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.263</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">108</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.7</td>
<td headers="Mega Salary (Over $10MM) stub_1_12 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.4 <span style="color:red">&#9660;</span></td></tr>
    <tr><th id="stub_1_13" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Manny Machado</th>
<td headers="Mega Salary (Over $10MM) stub_1_13 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">SDP</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$34M</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">156</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">153</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">640</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">28</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">92</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">106</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">12</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.8%</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">15.9%</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.290</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.278</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">122</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.2</td>
<td headers="Mega Salary (Over $10MM) stub_1_13 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.4 <span style="color:green">&#9650;</span></td></tr>
    <tr class="gt_group_heading_row">
      <th colspan="17" class="gt_group_heading" style="font-size: 13px; font-weight: bold;" scope="colgroup" id="Market-Value (3-$10MM)">Market-Value (3-$10MM)</th>
    </tr>
    <tr class="gt_row_group_first"><th id="stub_1_14" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Juan Soto</th>
<td headers="Market-Value (3-$10MM) stub_1_14 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">WSN</td>
<td headers="Market-Value (3-$10MM) stub_1_14 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$8M</td>
<td headers="Market-Value (3-$10MM) stub_1_14 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">150</td>
<td headers="Market-Value (3-$10MM) stub_1_14 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">151</td>
<td headers="Market-Value (3-$10MM) stub_1_14 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">654</td>
<td headers="Market-Value (3-$10MM) stub_1_14 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">29</td>
<td headers="Market-Value (3-$10MM) stub_1_14 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">111</td>
<td headers="Market-Value (3-$10MM) stub_1_14 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">95</td>
<td headers="Market-Value (3-$10MM) stub_1_14 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">9</td>
<td headers="Market-Value (3-$10MM) stub_1_14 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">22.2%</td>
<td headers="Market-Value (3-$10MM) stub_1_14 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">14.2%</td>
<td headers="Market-Value (3-$10MM) stub_1_14 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.332</td>
<td headers="Market-Value (3-$10MM) stub_1_14 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.313</td>
<td headers="Market-Value (3-$10MM) stub_1_14 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">163</td>
<td headers="Market-Value (3-$10MM) stub_1_14 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.9</td>
<td headers="Market-Value (3-$10MM) stub_1_14 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.6 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_15" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Jose Ramirez</th>
<td headers="Market-Value (3-$10MM) stub_1_15 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">CLE</td>
<td headers="Market-Value (3-$10MM) stub_1_15 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$9M</td>
<td headers="Market-Value (3-$10MM) stub_1_15 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">129</td>
<td headers="Market-Value (3-$10MM) stub_1_15 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">152</td>
<td headers="Market-Value (3-$10MM) stub_1_15 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">636</td>
<td headers="Market-Value (3-$10MM) stub_1_15 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">36</td>
<td headers="Market-Value (3-$10MM) stub_1_15 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">111</td>
<td headers="Market-Value (3-$10MM) stub_1_15 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">103</td>
<td headers="Market-Value (3-$10MM) stub_1_15 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">27</td>
<td headers="Market-Value (3-$10MM) stub_1_15 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.3%</td>
<td headers="Market-Value (3-$10MM) stub_1_15 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">13.7%</td>
<td headers="Market-Value (3-$10MM) stub_1_15 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.256</td>
<td headers="Market-Value (3-$10MM) stub_1_15 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.266</td>
<td headers="Market-Value (3-$10MM) stub_1_15 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">137</td>
<td headers="Market-Value (3-$10MM) stub_1_15 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.6</td>
<td headers="Market-Value (3-$10MM) stub_1_15 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.3 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_16" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Matt Olson</th>
<td headers="Market-Value (3-$10MM) stub_1_16 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">OAK</td>
<td headers="Market-Value (3-$10MM) stub_1_16 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$5M</td>
<td headers="Market-Value (3-$10MM) stub_1_16 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">127</td>
<td headers="Market-Value (3-$10MM) stub_1_16 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">156</td>
<td headers="Market-Value (3-$10MM) stub_1_16 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">673</td>
<td headers="Market-Value (3-$10MM) stub_1_16 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">39</td>
<td headers="Market-Value (3-$10MM) stub_1_16 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">101</td>
<td headers="Market-Value (3-$10MM) stub_1_16 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">111</td>
<td headers="Market-Value (3-$10MM) stub_1_16 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4</td>
<td headers="Market-Value (3-$10MM) stub_1_16 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">13.1%</td>
<td headers="Market-Value (3-$10MM) stub_1_16 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">16.8%</td>
<td headers="Market-Value (3-$10MM) stub_1_16 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.269</td>
<td headers="Market-Value (3-$10MM) stub_1_16 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.271</td>
<td headers="Market-Value (3-$10MM) stub_1_16 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">146</td>
<td headers="Market-Value (3-$10MM) stub_1_16 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.9</td>
<td headers="Market-Value (3-$10MM) stub_1_16 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.0 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_17" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Max Muncy</th>
<td headers="Market-Value (3-$10MM) stub_1_17 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">LAD</td>
<td headers="Market-Value (3-$10MM) stub_1_17 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$9M</td>
<td headers="Market-Value (3-$10MM) stub_1_17 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">141</td>
<td headers="Market-Value (3-$10MM) stub_1_17 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">144</td>
<td headers="Market-Value (3-$10MM) stub_1_17 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">592</td>
<td headers="Market-Value (3-$10MM) stub_1_17 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">36</td>
<td headers="Market-Value (3-$10MM) stub_1_17 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">95</td>
<td headers="Market-Value (3-$10MM) stub_1_17 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">94</td>
<td headers="Market-Value (3-$10MM) stub_1_17 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">2</td>
<td headers="Market-Value (3-$10MM) stub_1_17 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">14.0%</td>
<td headers="Market-Value (3-$10MM) stub_1_17 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">20.3%</td>
<td headers="Market-Value (3-$10MM) stub_1_17 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.257</td>
<td headers="Market-Value (3-$10MM) stub_1_17 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.249</td>
<td headers="Market-Value (3-$10MM) stub_1_17 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">140</td>
<td headers="Market-Value (3-$10MM) stub_1_17 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.7</td>
<td headers="Market-Value (3-$10MM) stub_1_17 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.9 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_18" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Rafael Devers</th>
<td headers="Market-Value (3-$10MM) stub_1_18 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">BOS</td>
<td headers="Market-Value (3-$10MM) stub_1_18 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$5M</td>
<td headers="Market-Value (3-$10MM) stub_1_18 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">156</td>
<td headers="Market-Value (3-$10MM) stub_1_18 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">156</td>
<td headers="Market-Value (3-$10MM) stub_1_18 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">664</td>
<td headers="Market-Value (3-$10MM) stub_1_18 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">38</td>
<td headers="Market-Value (3-$10MM) stub_1_18 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">101</td>
<td headers="Market-Value (3-$10MM) stub_1_18 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">113</td>
<td headers="Market-Value (3-$10MM) stub_1_18 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5</td>
<td headers="Market-Value (3-$10MM) stub_1_18 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.3%</td>
<td headers="Market-Value (3-$10MM) stub_1_18 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">21.5%</td>
<td headers="Market-Value (3-$10MM) stub_1_18 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.307</td>
<td headers="Market-Value (3-$10MM) stub_1_18 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.279</td>
<td headers="Market-Value (3-$10MM) stub_1_18 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">134</td>
<td headers="Market-Value (3-$10MM) stub_1_18 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.9</td>
<td headers="Market-Value (3-$10MM) stub_1_18 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.7 <span style="color:red">&#9660;</span></td></tr>
    <tr><th id="stub_1_19" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Yoan Moncada</th>
<td headers="Market-Value (3-$10MM) stub_1_19 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">CHW</td>
<td headers="Market-Value (3-$10MM) stub_1_19 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$7M</td>
<td headers="Market-Value (3-$10MM) stub_1_19 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">132</td>
<td headers="Market-Value (3-$10MM) stub_1_19 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">144</td>
<td headers="Market-Value (3-$10MM) stub_1_19 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">616</td>
<td headers="Market-Value (3-$10MM) stub_1_19 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">14</td>
<td headers="Market-Value (3-$10MM) stub_1_19 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">74</td>
<td headers="Market-Value (3-$10MM) stub_1_19 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">61</td>
<td headers="Market-Value (3-$10MM) stub_1_19 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">3</td>
<td headers="Market-Value (3-$10MM) stub_1_19 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">13.6%</td>
<td headers="Market-Value (3-$10MM) stub_1_19 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">25.5%</td>
<td headers="Market-Value (3-$10MM) stub_1_19 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.350</td>
<td headers="Market-Value (3-$10MM) stub_1_19 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.263</td>
<td headers="Market-Value (3-$10MM) stub_1_19 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">122</td>
<td headers="Market-Value (3-$10MM) stub_1_19 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.6</td>
<td headers="Market-Value (3-$10MM) stub_1_19 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.5 <span style="color:red">&#9660;</span></td></tr>
    <tr class="gt_group_heading_row">
      <th colspan="17" class="gt_group_heading" style="font-size: 13px; font-weight: bold;" scope="colgroup" id="Great Deal (1-$3MM)">Great Deal (1-$3MM)</th>
    </tr>
    <tr class="gt_row_group_first"><th id="stub_1_20" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Fernando Tatis Jr.</th>
<td headers="Great Deal (1-$3MM) stub_1_20 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">SDP</td>
<td headers="Great Deal (1-$3MM) stub_1_20 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$2M</td>
<td headers="Great Deal (1-$3MM) stub_1_20 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Great Deal (1-$3MM) stub_1_20 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">130</td>
<td headers="Great Deal (1-$3MM) stub_1_20 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">546</td>
<td headers="Great Deal (1-$3MM) stub_1_20 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">42</td>
<td headers="Great Deal (1-$3MM) stub_1_20 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">99</td>
<td headers="Great Deal (1-$3MM) stub_1_20 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">97</td>
<td headers="Great Deal (1-$3MM) stub_1_20 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">25</td>
<td headers="Great Deal (1-$3MM) stub_1_20 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.4%</td>
<td headers="Great Deal (1-$3MM) stub_1_20 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">28.0%</td>
<td headers="Great Deal (1-$3MM) stub_1_20 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.324</td>
<td headers="Great Deal (1-$3MM) stub_1_20 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.282</td>
<td headers="Great Deal (1-$3MM) stub_1_20 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">156</td>
<td headers="Great Deal (1-$3MM) stub_1_20 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Great Deal (1-$3MM) stub_1_20 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.1</td></tr>
    <tr><th id="stub_1_21" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Brandon Lowe</th>
<td headers="Great Deal (1-$3MM) stub_1_21 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">TBR</td>
<td headers="Great Deal (1-$3MM) stub_1_21 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$2M</td>
<td headers="Great Deal (1-$3MM) stub_1_21 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Great Deal (1-$3MM) stub_1_21 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">149</td>
<td headers="Great Deal (1-$3MM) stub_1_21 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">615</td>
<td headers="Great Deal (1-$3MM) stub_1_21 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">39</td>
<td headers="Great Deal (1-$3MM) stub_1_21 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">97</td>
<td headers="Great Deal (1-$3MM) stub_1_21 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">99</td>
<td headers="Great Deal (1-$3MM) stub_1_21 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">7</td>
<td headers="Great Deal (1-$3MM) stub_1_21 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.1%</td>
<td headers="Great Deal (1-$3MM) stub_1_21 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">27.2%</td>
<td headers="Great Deal (1-$3MM) stub_1_21 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.280</td>
<td headers="Great Deal (1-$3MM) stub_1_21 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.247</td>
<td headers="Great Deal (1-$3MM) stub_1_21 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">137</td>
<td headers="Great Deal (1-$3MM) stub_1_21 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Great Deal (1-$3MM) stub_1_21 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.2</td></tr>
    <tr><th id="stub_1_22" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Shohei Ohtani</th>
<td headers="Great Deal (1-$3MM) stub_1_22 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">LAA</td>
<td headers="Great Deal (1-$3MM) stub_1_22 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$3M</td>
<td headers="Great Deal (1-$3MM) stub_1_22 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Great Deal (1-$3MM) stub_1_22 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">158</td>
<td headers="Great Deal (1-$3MM) stub_1_22 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">639</td>
<td headers="Great Deal (1-$3MM) stub_1_22 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">46</td>
<td headers="Great Deal (1-$3MM) stub_1_22 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">103</td>
<td headers="Great Deal (1-$3MM) stub_1_22 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">100</td>
<td headers="Great Deal (1-$3MM) stub_1_22 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">26</td>
<td headers="Great Deal (1-$3MM) stub_1_22 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">15.0%</td>
<td headers="Great Deal (1-$3MM) stub_1_22 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">29.6%</td>
<td headers="Great Deal (1-$3MM) stub_1_22 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.303</td>
<td headers="Great Deal (1-$3MM) stub_1_22 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.257</td>
<td headers="Great Deal (1-$3MM) stub_1_22 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">152</td>
<td headers="Great Deal (1-$3MM) stub_1_22 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Great Deal (1-$3MM) stub_1_22 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.1</td></tr>
    <tr class="gt_group_heading_row">
      <th colspan="17" class="gt_group_heading" style="font-size: 13px; font-weight: bold;" scope="colgroup" id="Highway Robbery (&amp;lt; $1MM)">Highway Robbery (&lt; $1MM)</th>
    </tr>
    <tr class="gt_row_group_first"><th id="stub_1_23" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Vladimir Guerrero Jr.</th>
<td headers="Highway Robbery (< $1MM) stub_1_23 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">TOR</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$605K</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">123</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">161</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">698</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">48</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">123</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">111</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">12.3%</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">15.8%</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.313</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.311</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">166</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.4</td>
<td headers="Highway Robbery (< $1MM) stub_1_23 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">6.7 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_24" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Bryan Reynolds</th>
<td headers="Highway Robbery (< $1MM) stub_1_24 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">PIT</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$601K</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">134</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">159</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">646</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">24</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">93</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">90</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">11.6%</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">18.4%</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.345</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.302</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">142</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">3.2</td>
<td headers="Highway Robbery (< $1MM) stub_1_24 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.5 <span style="color:green">&#9650;</span></td></tr>
    <tr><th id="stub_1_25" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Tyler O'Neill</th>
<td headers="Highway Robbery (< $1MM) stub_1_25 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">STL</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$595K</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">138</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">537</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">34</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">89</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">80</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">15</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">7.1%</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">31.3%</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.366</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.286</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">145</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_25 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.4</td></tr>
    <tr><th id="stub_1_26" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Cedric Mullins II</th>
<td headers="Highway Robbery (< $1MM) stub_1_26 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">BAL</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$577K</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">159</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">675</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">30</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">91</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">59</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">30</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">8.7%</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">18.5%</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.322</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.291</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">136</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_26 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.3</td></tr>
    <tr><th id="stub_1_27" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Bo Bichette</th>
<td headers="Highway Robbery (< $1MM) stub_1_27 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">TOR</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$588K</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">159</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">690</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">29</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">121</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">102</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">25</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">5.8%</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">19.9%</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.339</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.298</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">122</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_27 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.9</td></tr>
    <tr><th id="stub_1_28" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Kyle Tucker</th>
<td headers="Highway Robbery (< $1MM) stub_1_28 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">HOU</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$624K</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">140</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">567</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">30</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">83</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">92</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">14</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">9.3%</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">15.9%</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.304</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.294</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">147</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_28 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.8</td></tr>
    <tr><th id="stub_1_29" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Jake Cronenworth</th>
<td headers="Highway Robbery (< $1MM) stub_1_29 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">SDP</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$585K</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">152</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">643</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">21</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">94</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">71</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">8.6%</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">14.0%</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.283</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.266</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">116</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_29 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.4</td></tr>
    <tr><th id="stub_1_30" scope="row" class="gt_row gt_left gt_stub" style="font-size: 12px; text-indent: 8px;">Nicky Lopez</th>
<td headers="Highway Robbery (< $1MM) stub_1_30 Team" class="gt_row gt_left" style="font-size: 12px; text-align: center;">KCR</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 Salary" class="gt_row gt_right" style="font-size: 12px; text-align: center;">$598K</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 G19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 G21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">151</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 PA" class="gt_row gt_right" style="font-size: 12px; text-align: center;">565</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 HR" class="gt_row gt_right" style="font-size: 12px; text-align: center;">2</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 R" class="gt_row gt_right" style="font-size: 12px; text-align: center;">78</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 RBI" class="gt_row gt_right" style="font-size: 12px; text-align: center;">43</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 SB" class="gt_row gt_right" style="font-size: 12px; text-align: center;">22</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 BB%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">8.7%</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 K%" class="gt_row gt_right" style="font-size: 12px; text-align: center;">13.1%</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 BABIP" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.347</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 AVG" class="gt_row gt_right" style="font-size: 12px; text-align: center;">0.300</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 wRC+" class="gt_row gt_right" style="font-size: 12px; text-align: center;">106</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 WAR19" class="gt_row gt_right" style="font-size: 12px; text-align: center;">NA</td>
<td headers="Highway Robbery (< $1MM) stub_1_30 WAR21" class="gt_row gt_right" style="font-size: 12px; text-align: center;">4.4</td></tr>
  </tbody>
  <tfoot class="gt_sourcenotes">
    <tr>
      <td class="gt_sourcenote" colspan="17"><em>Source: FanGraphs</em></td>
    </tr>
  </tfoot>
  &#10;</table>
</div>
