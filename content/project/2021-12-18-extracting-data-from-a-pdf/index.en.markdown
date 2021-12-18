---
title: Extracting data from a pdf
author: twg
date: '2021-12-18'
slug: extracting-data-from-a-pdf
categories:
  - data ingest
  - pdf
tags:
  - data ingest
  - pdf
subtitle: ''
summary: 'An example of how to read in and clean data from a pdf'
authors: []
lastmod: '2021-12-18T10:26:07-08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: yes
projects: []

---



{{% toc %}}

Using the `pdftools` package to read in pdf data. In this case, reading in a pdf of [survey data](https://assets.morningconsult.com/wp-uploads/2021/10/18170121/2110046_crosstabs_MC_ENTERTAINMENT_PRESTIGE_TV_Adults_v1_DM-1-1.pdf) from Morning Consult. 

## Reading the data 

The first thing to do is use the `pdf_text()` function from the pdftools package to read the pdf into our environment. The pdf can either be downloaded first, or we can just use the url. Regardless, `pdf_text()` reads in the entire pdf.  


```r
pacman::p_load(tidyverse, janitor, here, glue, pdftools)
url <- "https://assets.morningconsult.com/wp-uploads/2021/10/18170121/2110046_crosstabs_MC_ENTERTAINMENT_PRESTIGE_TV_Adults_v1_DM-1-1.pdf"

tmp <- pdftools::pdf_text("~/Desktop/streaming.pdf")
head(tmp)
```


```
## [1] "                            National Tracking Poll #2110046\n                                 October 09-12, 2021\n\n                                  Crosstabulation Results\n\n\n\n\nMethodology:\nThis poll was conducted between October 9-October 12, 2021 among a sample of 2200 Adults. The\ninterviews were conducted online. Results from the full survey have a margin of error of plus or\nminus 2 percentage points.\n"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
## [2] "Table Index\n\n  1    Table MCEN6_1: Thinking about the quality of each streaming service, do you consider each\n       of the following to have above average, average, or below average reputation? Netflix . . . . .       3\n\n  2    Table MCEN6_2: Thinking about the quality of each streaming service, do you consider each\n       of the following to have above average, average, or below average reputation? Hulu . . . . .          7\n\n  3    Table MCEN6_3: Thinking about the quality of each streaming service, do you consider each\n       of the following to have above average, average, or below average reputation? HBO Max . . .          11\n\n  4    Table MCEN6_4: Thinking about the quality of each streaming service, do you consider each\n       of the following to have above average, average, or below average reputation? Disney+ . . . .        15\n\n  5    Table MCEN6_5: Thinking about the quality of each streaming service, do you consider each\n       of the following to have above average, average, or below average reputation? Apple TV+ . .          19\n\n  6    Table MCEN6_6: Thinking about the quality of each streaming service, do you consider each\n       of the following to have above average, average, or below average reputation? Paramount+ .           23\n\n  7    Table MCEN6_7: Thinking about the quality of each streaming service, do you consider each\n       of the following to have above average, average, or below average reputation? Amazon Prime\n       Video . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .    27\n\n  8    Table MCEN6_8: Thinking about the quality of each streaming service, do you consider each\n       of the following to have above average, average, or below average reputation? Peacock . . . .        31\n\n  9    Table MCEN6_9: Thinking about the quality of each streaming service, do you consider each\n       of the following to have above average, average, or below average reputation? Showtime . . .         35\n\n  10   Table MCEN6_10: Thinking about the quality of each streaming service, do you consider\n       each of the following to have above average, average, or below average reputation? Starz . . .       39\n\n  11   Table MCEN8: Which streaming service/network do you feel, on average, has the highest\n       quality shows? . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .   43\n\n  12   Table MCEN9: Which streaming service/network has the most high-quality shows on its plat-\n       form? . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .    46\n\n  13   Summary Statistics of Survey Respondent Demographics . . . . . . . . . . . . . . . . .               49\n\n\n\n\n                                                     2\n"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        
## [3] "National Tracking Poll #2110046, October, 2021\nTable MCEN6_1\n\n\n                                     Crosstabulation Results by Respondent Demographics\n\nTable MCEN6_1: Thinking about the quality of each streaming service, do you consider each of the following to have above average, average, or below\naverage reputation?\nNetflix\n                                                                                                          Don’t know / No\n            Demographic                          Above average         Average            Below average      opinion                 Total N\n  Adults                                    57%        (1248)    26%       (578)      5%          (111)   12%      (263)                   2200\n  Gender: Male                              57%         (607)    26%       (274)      5%          (53)    12%      (127)                   1062\n  Gender: Female                            56%         (641)    27%       (303)      5%          (58)    12%      (135)                    1138\n  Age: 18-34                                61%         (398)    30%       (197)      5%           (31)     4%      (29)                     655\n  Age: 35-44                                60%         (216)    29%       (103)      3%           (12)     7%       (27)                    358\n  Age: 45-64                                58%         (435)    22%       (168)      7%          (53)    13%       (94)                     751\n  Age: 65+                                  46%         (199)    25%        (110)     3%           (15)   26%       (113)                   436\n  GenZers: 1997-2012                        61%          (123)   26%         (52)     7%          (14)      6%       (13)                    201\n  Millennials: 1981-1996                    61%         (410)    30%       (204)      4%          (27)      5%       (32)                    673\n  GenXers: 1965-1980                        59%         (330)    25%        (137)     6%          (32)    10%       (56)                     555\n  Baby Boomers: 1946-1964                   52%         (362)    25%        (173)     5%          (38)    18%      (125)                    698\n  PID: Dem (no lean)                        62%         (529)    24%       (204)      4%          (37)    10%       (82)                     851\n  PID: Ind (no lean)                        53%         (386)    30%       (221)      5%          (33)    13%       (92)                     731\n  PID: Rep (no lean)                        54%         (334)    25%        (153)     7%          (41)    14%       (89)                     617\n  PID/Gender: Dem Men                       63%         (249)    24%         (93)     4%          (16)      9%       (37)                    395\n  PID/Gender: Dem Women                     61%         (280)    24%         (111)    5%           (21)   10%       (45)                     457\n  PID/Gender: Ind Men                       52%         (189)    30%       (109)      4%           (15)   13%       (48)                     361\n  PID/Gender: Ind Women                     53%         (197)    30%        (112)     5%          (19)    12%       (43)                     370\n  PID/Gender: Rep Men                       55%         (169)    23%         (72)     7%          (22)    14%       (42)                    306\n  PID/Gender: Rep Women                     53%         (165)    26%          (81)    6%          (19)    15%       (47)                     312\n  Ideo: Liberal (1-3)                       66%         (425)    23%        (151)     4%          (25)      7%      (43)                    644\n  Ideo: Moderate (4)                        53%         (343)    28%       (179)      3%           (21)   15%       (98)                     641\n  Ideo: Conservative (5-7)                  53%         (366)    26%       (178)      8%          (52)    13%       (92)                    688\n  Educ: < College                           54%         (823)    27%       (413)      5%          (80)    13%      (196)                    1512\n  Educ: Bachelors degree                    64%         (283)    23%       (100)      5%          (20)      9%      (40)                    444\n  Educ: Post-grad                           58%         (142)    27%         (65)     5%           (11)    11%      (26)                    244\n                                                                 Continued on next page\n\n\n\n                                                                                                                                                   3\n"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        
## [4] "                                                                                                                                    Morning Consult\n                                                                                                                                    Table MCEN6_1\n\nTable MCEN6_1: Thinking about the quality of each streaming service, do you consider each of the following to have above average, average, or below\naverage reputation?\nNetflix\n                                                                                                          Don’t know / No\n          Demographic                  Above average              Average              Below average         opinion                 Total N\n Adults                               57%     (1248)        26%       (578)         5%         (111)     12%      (263)                    2200\n Income: Under 50k                    54%      (636)        27%       (324)         5%         (57)      14%      (169)                     1187\n Income: 50k-100k                     58%      (390)        25%       (168)         7%         (46)      10%        (71)                     675\n Income: 100k+                        66%      (222)        25%         (85)        2%           (8)       7%       (23)                     338\n Ethnicity: White                     56%      (958)        26%       (448)         5%         (86)      13%      (230)                    1722\n Ethnicity: Hispanic                  57%      (198)        31%       (109)         5%          (18)       7%       (25)                     349\n Ethnicity: Black                     63%      (174)        27%         (73)        4%          (12)       6%       (15)                     274\n Ethnicity: Other                     57%       (116)       28%         (57)        7%         (14)        9%       (17)                     204\n All Christian                        56%      (563)        25%       (250)         5%         (53)      13%      (132)                      998\n All Non-Christian                    52%       (69)        30%         (39)        7%           (9)      11%       (15)                     132\n Atheist                              64%        (67)       21%         (22)        7%           (7)       8%        (9)                     104\n Agnostic/Nothing in particular       57%      (338)        26%        (157)        5%         (28)      12%       (70)                      594\n Something Else                       57%       (211)       30%        (110)        4%         (14)      10%        (37)                     372\n Religious Non-Protestant/Catholic    52%        (79)       30%         (46)        8%          (12)     10%        (15)                      151\n Evangelical                          56%      (297)        29%        (152)        5%         (28)      10%       (54)                      531\n Non-Evangelical                      57%      (459)        24%       (195)         4%         (34)      14%       (111)                     799\n Community: Urban                     60%      (388)        27%       (176)         5%         (33)        8%      (49)                     646\n Community: Suburban                  56%      (573)        27%       (276)         4%         (42)      13%      (127)                     1018\n Community: Rural                     54%      (287)        24%       (126)         7%         (36)      16%       (86)                      536\n Employ: Private Sector               63%      (474)        24%        (181)        6%         (46)        6%      (47)                      747\n Employ: Government                   67%        (83)       26%         (32)        1%           (1)       6%        (7)                     124\n Employ: Self-Employed                48%      (102)        36%         (76)        4%           (9)      11%      (24)                       211\n Employ: Homemaker                    48%        (62)       34%         (44)        5%           (7)     13%        (16)                     130\n Employ: Student                      69%        (42)       20%         (12)        6%           (4)       5%        (3)                       61\n Employ: Retired                      47%      (237)        24%        (122)        5%         (24)      24%       (121)                     504\n Employ: Unemployed                   58%      (162)        25%         (71)        5%          (15)      11%       (31)                     279\n Employ: Other                        59%        (85)       28%         (40)        4%           (6)       9%       (13)                     144\n Military HH: Yes                     56%      (189)        27%         (90)        8%         (26)      10%        (33)                     338\n Military HH: No                      57%     (1060)        26%       (488)         5%         (86)      12%      (229)                    1862\n                                                              Continued on next page\n\n\n\n                                                                                                                                                    4\n"
## [5] "National Tracking Poll #2110046, October, 2021\nTable MCEN6_1\n\nTable MCEN6_1: Thinking about the quality of each streaming service, do you consider each of the following to have above average, average, or below\naverage reputation?\nNetflix\n                                                                                                          Don’t know / No\n            Demographic                          Above average         Average            Below average      opinion                 Total N\n  Adults                                    57%        (1248)    26%       (578)       5%         (111)   12%      (263)                   2200\n  RD/WT: Right Direction                    62%         (532)    22%       (192)       4%         (34)    12%      (106)                    863\n  RD/WT: Wrong Track                        54%         (717)    29%       (386)       6%         (77)    12%      (157)                   1337\n  Biden Job Approve                         62%         (643)    23%       (238)       4%         (38)    12%      (120)                   1039\n  Biden Job Disapprove                      52%         (548)    29%        (311)      7%          (71)   12%      (130)                   1060\n  Biden Job Strongly Approve                66%         (336)    19%         (97)      3%          (17)   12%       (60)                     510\n  Biden Job Somewhat Approve                58%         (307)    27%        (141)      4%          (21)    11%       (61)                   529\n  Biden Job Somewhat Disapprove             59%         (183)    27%         (82)      3%         (10)     11%       (34)                    310\n  Biden Job Strongly Disapprove             49%         (365)    31%       (229)       8%         (61)    13%        (95)                   750\n  Favorable of Biden                        63%         (661)    23%       (248)       3%         (34)     11%      (114)                  1057\n  Unfavorable of Biden                      53%         (550)    28%       (295)       7%         (73)    12%      (126)                   1045\n  Very Favorable of Biden                   64%         (355)    19%       (104)       3%          (18)   14%        (75)                   553\n  Somewhat Favorable of Biden               61%         (306)    28%       (143)       3%         (16)      8%       (39)                   504\n  Somewhat Unfavorable of Biden             58%         (147)    25%         (64)      6%          (15)    11%       (28)                   254\n  Very Unfavorable of Biden                 51%         (403)    29%        (231)      7%         (58)    12%        (99)                    791\n  #1 Issue: Economy                         60%         (498)    28%       (230)       3%         (29)      9%       (71)                   828\n  #1 Issue: Security                        52%         (165)    26%         (84)      7%         (22)    15%        (48)                    319\n  #1 Issue: Health Care                     61%          (161)   25%         (67)      6%         (16)      8%       (20)                   265\n  #1 Issue: Medicare / Social Security      49%         (147)    23%         (70)      4%          (11)   24%        (72)                   300\n  #1 Issue: Women’s Issues                  62%           (99)   30%         (48)      4%           (7)     5%        (7)                    161\n  #1 Issue: Education                       50%           (48)   29%         (28)     10%         (10)     11%       (10)                     96\n  #1 Issue: Energy                          62%           (70)   23%         (25)      2%           (3)   13%        (14)                    112\n  #1 Issue: Other                           51%           (61)   21%         (25)     12%         (14)    17%        (20)                    119\n  2020 Vote: Joe Biden                      62%         (622)    24%       (241)       4%         (41)     11%     (108)                    1011\n  2020 Vote: Donald Trump                   51%         (361)    28%       (200)       7%         (47)    14%        (98)                   706\n  2020 Vote: Other                          55%           (34)   28%         (17)      8%           (5)     9%        (6)                     62\n  2020 Vote: Didn’t Vote                    54%         (224)    29%       (120)       5%         (19)    12%        (51)                    413\n  2018 House Vote: Democrat                 63%         (498)    23%       (187)       3%         (27)    10%        (84)                   796\n  2018 House Vote: Republican               53%          (313)   25%       (144)       8%         (47)    14%        (83)                   587\n  2018 House Vote: Someone else             42%           (27)   35%         (23)      6%           (4)   18%        (12)                     65\n                                                                 Continued on next page\n\n                                                                                                                                                   5\n"                                                                                                          
## [6] "                                                                                                                                        Morning Consult\n                                                                                                                                        Table MCEN6_1\n\nTable MCEN6_1: Thinking about the quality of each streaming service, do you consider each of the following to have above average, average, or below\naverage reputation?\nNetflix\n                                                                                                             Don’t know / No\n          Demographic                   Above average               Average            Below average            opinion                 Total N\n Adults                                57%     (1248)         26%       (578)         5%        (111)        12%      (263)                       2200\n 2016 Vote: Hillary Clinton            64%      (457)         23%       (164)         3%        (22)         10%       (75)                        719\n 2016 Vote: Donald Trump               53%      (347)         26%       (170)         9%        (58)         13%       (84)                        659\n 2016 Vote: Other                      50%       (46)         27%         (25)        4%          (4)        19%       (17)                         91\n 2016 Vote: Didn’t Vote                54%      (396)         30%       (218)         4%        (28)         12%       (87)                        728\n Voted in 2014: Yes                    59%      (741)         23%       (293)         5%        (69)         12%      (156)                       1259\n Voted in 2014: No                     54%      (507)         30%       (285)         5%        (43)         11%      (107)                        941\n 4-Region: Northeast                   59%      (232)         24%        (96)         4%        (16)         13%       (50)                        394\n 4-Region: Midwest                     57%      (263)         25%        (117)        4%         (21)        13%       (62)                        462\n 4-Region: South                       56%      (459)         27%       (219)         7%        (54)         11%       (93)                        824\n 4-Region: West                        57%      (295)         28%       (146)         4%         (21)        11%       (58)                        520\n Note: Row proportions may total to larger than one-hundred percent due to rounding. For more information visit MorningConsultIntelligence.com.\n\n\n\n\n                                                                                                                                                         6\n"
```

As the [original post](https://ropensci.org/blog/2016/03/01/pdftools-and-jeroen/) for pdftools says, the pdf format is pretty dumb. It doesn't understand tabular format, it's just text that's been strategically placed in order to approximate a table. When pdftools reads in a pdf, it reads in the text (while trying to preserve spacing and formatting), but it will then be up to us to decide how we parse what we want. That said, pdftools does preserve spacing pretty well, and it retains paging. This means that if we want to get data out of a specific page, we can simply call the page number by direct reference. If our pdf has a consistent structure and format, then we can write a function to read in all pages.  


```r
cat(tmp[7]) 
```

```
## National Tracking Poll #2110046, October, 2021
## Table MCEN6_2
## 
## Table MCEN6_2: Thinking about the quality of each streaming service, do you consider each of the following to have above average, average, or below
## average reputation?
## Hulu
##                                                                                                           Don’t know / No
##             Demographic                          Above average         Average            Below average      opinion                 Total N
##   Adults                                    39%         (857)    34%      (755)        6%        (139)    20%     (448)                    2200
##   Gender: Male                              38%         (402)    34%      (363)        8%         (81)    20%     (216)                    1062
##   Gender: Female                            40%         (455)    34%      (392)        5%         (58)    20%     (232)                     1138
##   Age: 18-34                                53%         (348)    32%      (209)        6%         (40)      9%      (58)                     655
##   Age: 35-44                                43%         (154)    34%       (121)       6%         (21)    17%      (62)                      358
##   Age: 45-64                                35%         (262)    36%       (271)       7%         (55)    22%     (163)                      751
##   Age: 65+                                  21%           (93)   36%       (155)       5%         (23)    38%     (165)                      436
##   GenZers: 1997-2012                        51%         (102)    32%        (64)       7%         (14)    10%       (21)                     201
##   Millennials: 1981-1996                    51%         (343)    32%      (216)        6%         (39)     11%     (74)                      673
##   GenXers: 1965-1980                        39%         (216)    34%      (190)        8%         (45)    19%     (104)                      555
##   Baby Boomers: 1946-1964                   26%          (183)   38%      (264)        6%         (40)    30%     (210)                     698
##   PID: Dem (no lean)                        46%         (394)    32%      (273)        5%         (41)    17%     (144)                      851
##   PID: Ind (no lean)                        34%         (248)    39%      (288)        6%         (46)    20%     (149)                      731
##   PID: Rep (no lean)                        35%          (215)   31%      (194)        9%         (53)    25%     (156)                      617
##   PID/Gender: Dem Men                       47%          (185)   31%       (123)       6%         (22)    16%      (65)                      395
##   PID/Gender: Dem Women                     46%         (209)    33%       (150)       4%         (19)    17%      (79)                      457
##   PID/Gender: Ind Men                       32%          (117)   39%      (140)        8%         (29)    21%      (76)                      361
##   PID/Gender: Ind Women                     36%          (132)   40%      (149)        4%         (16)    20%       (73)                     370
##   PID/Gender: Rep Men                       33%          (101)   33%      (100)       10%         (29)    25%      (76)                      306
##   PID/Gender: Rep Women                     37%          (114)   30%        (94)       8%         (24)    26%      (80)                      312
##   Ideo: Liberal (1-3)                       49%          (316)   34%      (219)        4%         (25)    13%       (83)                    644
##   Ideo: Moderate (4)                        35%         (222)    35%      (226)        7%         (44)    23%     (150)                      641
##   Ideo: Conservative (5-7)                  36%         (245)    32%       (221)       8%         (57)    24%     (165)                      688
##   Educ: < College                           38%         (580)    33%       (501)       7%         (99)    22%     (332)                     1512
##   Educ: Bachelors degree                    42%          (187)   35%       (156)       6%         (26)    17%      (74)                     444
##   Educ: Post-grad                           37%           (91)   40%        (98)       6%         (14)    17%       (41)                    244
##   Income: Under 50k                         38%         (449)    34%      (400)        5%         (63)    23%     (274)                     1187
##   Income: 50k-100k                          40%         (268)    34%       (231)       8%         (55)    18%      (121)                     675
##   Income: 100k+                             41%         (140)    37%       (125)       6%         (21)    16%       (53)                     338
##   Ethnicity: White                          37%         (641)    35%      (594)        7%        (117)    21%     (369)                    1722
##                                                                  Continued on next page
## 
##                                                                                                                                                    7
```

## Cleaning the data

In order to get the data into a workable format, we'll have to use regex to add delimiters that identify the location of each column. With a pdf that is consistently formatted, this is relatively easy. 

Each pdf will be different, but the process is generally the same. We'll first pick a representative page, isolate it, and then start working through it. The first thing to do is to split on the newline `\n` so that we can isolate specific rows of data. Below, we can see that the first lines identify the table, but the table itself doesn't start until line 8. 


```r
# which page to read in ##
tmp_pg <- tmp[7]
# split on the newline \n ##
tmp_table <- str_split(tmp_pg, "\n", simplify = TRUE)
tmp_table[1:10] 
```

```
##  [1] "National Tracking Poll #2110046, October, 2021"                                                                                                     
##  [2] "Table MCEN6_2"                                                                                                                                      
##  [3] ""                                                                                                                                                   
##  [4] "Table MCEN6_2: Thinking about the quality of each streaming service, do you consider each of the following to have above average, average, or below"
##  [5] "average reputation?"                                                                                                                                
##  [6] "Hulu"                                                                                                                                               
##  [7] "                                                                                                          Don’t know / No"                          
##  [8] "            Demographic                          Above average         Average            Below average      opinion                 Total N"       
##  [9] "  Adults                                    39%         (857)    34%      (755)        6%        (139)    20%     (448)                    2200"    
## [10] "  Gender: Male                              38%         (402)    34%      (363)        8%         (81)    20%     (216)                    1062"
```

With a pdf like this, the goal is to write a function that reads in all pages, so we need to identify where the table data starts and stops. Here, we've identified the start as the line containing the term "Demographic" and the table stops when we see the line containing "Continued" or "Note:" (note that we're setting the boundary on "Demographic" so that it doesn't potentially pick up "Demographics").  


```r
# which page to read in #
tmp_pg <- tmp[7]
# split on the newline \n #
tmp_table <- str_split(tmp_pg, "\n", simplify = TRUE)
# determine where the data stops and starts # 
tbl_start <- which(str_detect(tmp_table, "Demographic\\b")) # set boundary
  tbl_stop <- which(str_detect(tmp_table, "Continued|Note:")) # two endings depending on the page
# pull the table number # 
table_num <- tmp_table[,2]
# keep rows between our start and stop #  
tbl1 <- tmp_table[,(tbl_start+1):(tbl_stop-1)]
head(tbl1)
```

```
## [1] "  Adults                                    39%         (857)    34%      (755)        6%        (139)    20%     (448)                    2200" 
## [2] "  Gender: Male                              38%         (402)    34%      (363)        8%         (81)    20%     (216)                    1062" 
## [3] "  Gender: Female                            40%         (455)    34%      (392)        5%         (58)    20%     (232)                     1138"
## [4] "  Age: 18-34                                53%         (348)    32%      (209)        6%         (40)      9%      (58)                     655"
## [5] "  Age: 35-44                                43%         (154)    34%       (121)       6%         (21)    17%      (62)                      358"
## [6] "  Age: 45-64                                35%         (262)    36%       (271)       7%         (55)    22%     (163)                      751"
```

After isolating our table data, it's time to add delimiters. The delimiters will identify the location of each column in every row. If these are inconsistent then the table will not hold it's form.   


```r
# which page to read in #
tmp_pg <- tmp[7]
# split on the newline \n #
tmp_table <- str_split(tmp_pg, "\n", simplify = TRUE)
# determine where the data stops and starts # 
tbl_start <- which(str_detect(tmp_table, "Demographic\\b")) # set boundary
  tbl_stop <- which(str_detect(tmp_table, "Continued|Note:")) # two endings depending on the page
# pull the table number # 
table_num <- tmp_table[,2]
# keep rows between our start and stop #  
tbl1 <- tmp_table[,(tbl_start+1):(tbl_stop-1)]
# get rid of white space # 
tbl1 <- str_trim(tbl1)
# any time there are 2 spaces, add delimiter # 
tbl2 <- str_replace_all(tbl1, "\\s{2,}", "|")
# if % with space, add delimiter after
tbl2 <- str_replace_all(tbl2, "% ", "%|")
# if closing parentheses, add delimiter after
tbl2 <- str_replace_all(tbl2, "\\) ", ")|")
head(tbl2)
```

```
## [1] "Adults|39%|(857)|34%|(755)|6%|(139)|20%|(448)|2200"       
## [2] "Gender: Male|38%|(402)|34%|(363)|8%|(81)|20%|(216)|1062"  
## [3] "Gender: Female|40%|(455)|34%|(392)|5%|(58)|20%|(232)|1138"
## [4] "Age: 18-34|53%|(348)|32%|(209)|6%|(40)|9%|(58)|655"       
## [5] "Age: 35-44|43%|(154)|34%|(121)|6%|(21)|17%|(62)|358"      
## [6] "Age: 45-64|35%|(262)|36%|(271)|7%|(55)|22%|(163)|751"
```

It looks as if our delimiters are consistent across our would be rows, so now we can use the `textConnection()` function to tell R that our cleaned data should be read in as a text file. We then use `read.csv()` and specify our delimiter. 


```r
## which page to read in ##
tmp_pg <- tmp[7]
## split on the newline \n ##
tmp_table <- str_split(tmp_pg, "\n", simplify = TRUE)
## determine where the data stops and starts ## 
tbl_start <- which(str_detect(tmp_table, "Demographic\\b")) # set boundary
  tbl_stop <- which(str_detect(tmp_table, "Continued|Note:")) # two endings depending on the page
## pull the table number ## 
table_num <- tmp_table[,2]
## keep rows between our start and stop ##  
tbl1 <- tmp_table[,(tbl_start+1):(tbl_stop-1)]
## get rid of white space ## 
tbl1 <- str_trim(tbl1)
## any time there are 2 spaces, add delimiter ## 
tbl2 <- str_replace_all(tbl1, "\\s{2,}", "|")
tbl2 <- str_replace_all(tbl2, "% ", "%|")
tbl2 <- str_replace_all(tbl2, "\\) ", ")|")
## define table as text connection and read in with read.csv 
text_con <- textConnection(tbl2)
data_table <- read.csv(text_con, sep = "|", header = FALSE)
head(data_table)
```

```
##               V1  V2    V3  V4    V5 V6    V7  V8    V9  V10
## 1         Adults 39% (857) 34% (755) 6% (139) 20% (448) 2200
## 2   Gender: Male 38% (402) 34% (363) 8%  (81) 20% (216) 1062
## 3 Gender: Female 40% (455) 34% (392) 5%  (58) 20% (232) 1138
## 4     Age: 18-34 53% (348) 32% (209) 6%  (40)  9%  (58)  655
## 5     Age: 35-44 43% (154) 34% (121) 6%  (21) 17%  (62)  358
## 6     Age: 45-64 35% (262) 36% (271) 7%  (55) 22% (163)  751
```

Now, we'll just write a function to strip out all punctuation and convert our data to numeric. 


```r
# which page to read in #
tmp_pg <- tmp[7]
# split on the newline \n #
tmp_table <- str_split(tmp_pg, "\n", simplify = TRUE)
# determine where the data stops and starts # 
tbl_start <- which(str_detect(tmp_table, "Demographic\\b")) # set boundary
  tbl_stop <- which(str_detect(tmp_table, "Continued|Note:")) # two endings depending on the page
# pull the table number # 
table_num <- tmp_table[,2]
# keep rows between our start and stop #  
tbl1 <- tmp_table[,(tbl_start+1):(tbl_stop-1)]
# get rid of white space # 
tbl1 <- str_trim(tbl1)
# any time there are 2 spaces, add delimiter # 
tbl2 <- str_replace_all(tbl1, "\\s{2,}", "|")
tbl2 <- str_replace_all(tbl2, "% ", "%|")
tbl2 <- str_replace_all(tbl2, "\\) ", ")|")
# define table as text connection and read in with read.csv 
text_con <- textConnection(tbl2)
data_table <- read.csv(text_con, sep = "|", header = FALSE)

# function to strip out the punctuation
stripPunct <- function(x) as.numeric(str_replace_all(x, "%|\\(|\\)", ""))
# clean up and set as numeric # 
data_table <- data_table %>% 
  mutate(across(V2:ncol(.), stripPunct)) %>% 
  mutate(table_num = str_trim(table_num)) %>% 
  relocate(table_num) %>% 
  as_tibble()
head(data_table)
```

```
## # A tibble: 6 × 11
##   table_num     V1            V2    V3    V4    V5    V6    V7    V8    V9   V10
##   <chr>         <chr>      <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
## 1 Table MCEN6_2 Adults        39   857    34   755     6   139    20   448  2200
## 2 Table MCEN6_2 Gender: M…    38   402    34   363     8    81    20   216  1062
## 3 Table MCEN6_2 Gender: F…    40   455    34   392     5    58    20   232  1138
## 4 Table MCEN6_2 Age: 18-34    53   348    32   209     6    40     9    58   655
## 5 Table MCEN6_2 Age: 35-44    43   154    34   121     6    21    17    62   358
## 6 Table MCEN6_2 Age: 45-64    35   262    36   271     7    55    22   163   751
```

The one thing that's missing are the column names. With Morning Consult data, column names are sometimes broken up into two lines, which makes it very, very difficult to programmatically pull them out consistently. Instead, what we'll do is isolate the pages for a specific question, and pull the names for that question. We'll then set our names, making sure to cover two columns with each name (percentage and respondent count). 


```r
set_responses <- function(res) {
  ## set the names for table ## 
  response_count <- length(res) # how many colnames
  app <- c("_pct", "_n")
  # rep(app, response_count)
  nms <- c('Question', "Demographics", paste0(rep(res, each = 2), rep(app, response_count)), "Total")
  return(nms)
}
```

### Put it all together

The MC survey we've been working with asks a question about the reputation of a variety of different streaming services. The question format is the same across 10 different providers and the data can be found from pages 3 through 42. We can use the function below to get all of that data and put it into a tidy datset. normally, we would keep the whole dataset, but instead, we'll add the provider names and pull out the opinions of Adults. It looks as if Netflix is seen as the best provider.


```r
set_responses <- function(res) {
  # set the names for data table # 
  response_count <- length(res)
  app <- c("_pct", "_n")
  rep(app, response_count)
  nms <- c('Question', "Demographics", paste0(rep(res, each = 2), rep(app, response_count)), "Total")
  return(nms)
}

tidy_mc <- function(page) {
  
  # function to strip out the punctuation
  stripPunct <- function(x) suppressWarnings(as.numeric(str_replace_all(x, "%|\\(|\\)", "")))
  
  # which page to read in #
  tmp_pg <- tmp[page]
  # split on the newline \n #
  tmp_table <- str_split(tmp_pg, "\n", simplify = TRUE)
  # determine where the data stops and starts # 
  tbl_start <- which(str_detect(tmp_table, "Demographic\\b")) # set boundary
  tbl_stop <- which(str_detect(tmp_table, "Continued|Note:")) # two endings depending on the page
  # pull the table number # 
  table_num <- tmp_table[,2]
  # keep rows between our start and stop #  
  tbl1 <- tmp_table[,(tbl_start+1):(tbl_stop-1)]
  # get rid of white space # 
  tbl1 <- str_trim(tbl1)
  # any time there are 2 spaces, add delimiter # 
  tbl2 <- str_replace_all(tbl1, "\\s{2,}", "|")
  tbl2 <- str_replace_all(tbl2, "% ", "%|")
  tbl2 <- str_replace_all(tbl2, "\\) ", ")|")
  # define table as text connection and read in with read.csv 
  text_con <- textConnection(tbl2)
  data_table <- read.csv(text_con, sep = "|", header = FALSE)
  
  # clean up and set as numeric # 
  data_table <- data_table %>% 
    mutate(across(V2:ncol(.), stripPunct)) %>% 
    mutate(table_num = str_trim(table_num)) %>% 
    relocate(table_num) # new dplyr 1.0 functions
}

pages <- 3:42
title <- "Thinking about the quality of each streaming service, what do you consider the reputation of each?"
resp <- c("Above average", "Average", "Below average", "Don't know")
lvls <- c("Above average", "Average", "Below average", "Don't know")
# set the responses 
nms <- set_responses(resp)
## get the data and set responses
df <- pages %>% 
  map_dfr(tidy_mc) %>% 
  set_names(nms) %>% 
  mutate(Question = case_when(Question == "Table MCEN6_1" ~ "Netflix",
                              Question == "Table MCEN6_2" ~ "Hulu",
                              Question == "Table MCEN6_3" ~ "HBO Max",
                              Question == "Table MCEN6_4" ~ "Disney+",
                              Question == "Table MCEN6_5" ~ "Apple TV+",
                              Question == "Table MCEN6_6" ~ "Paramount+",
                              Question == "Table MCEN6_7" ~ "Amazon Prime Video",
                              Question == "Table MCEN6_8" ~ "Peacock+",
                              Question == "Table MCEN6_9" ~ "Showtime",
                              Question == "Table MCEN6_10" ~ "Starz")) %>% 
  filter(Demographics == "Adults") %>% 
  distinct(Question, Demographics, .keep_all = TRUE)

df %>% 
  knitr::kable(caption = title)
```



Table: Table 1: Thinking about the quality of each streaming service, what do you consider the reputation of each?

|Question           |Demographics | Above average_pct| Above average_n| Average_pct| Average_n| Below average_pct| Below average_n| Don't know_pct| Don't know_n| Total|
|:------------------|:------------|-----------------:|---------------:|-----------:|---------:|-----------------:|---------------:|--------------:|------------:|-----:|
|Netflix            |Adults       |                57|            1248|          26|       578|                 5|             111|             12|          263|  2200|
|Hulu               |Adults       |                39|             857|          34|       755|                 6|             139|             20|          448|  2200|
|HBO Max            |Adults       |                34|             752|          30|       655|                 7|             157|             29|          635|  2200|
|Disney+            |Adults       |                41|             899|          30|       657|                 6|             141|             23|          504|  2200|
|Apple TV+          |Adults       |                19|             415|          31|       676|                10|             224|             40|          886|  2200|
|Paramount+         |Adults       |                18|             405|          36|       797|                 9|             196|             36|          802|  2200|
|Amazon Prime Video |Adults       |                41|             911|          34|       751|                 5|             120|             19|          417|  2200|
|Peacock+           |Adults       |                18|             389|          37|       809|                11|             253|             34|          749|  2200|
|Showtime           |Adults       |                17|             364|          38|       844|                11|             236|             34|          755|  2200|
|Starz              |Adults       |                16|             342|          36|       790|                12|             274|             36|          794|  2200|



