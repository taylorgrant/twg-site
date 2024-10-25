---
title: Chi-Square prop testing
author: twg
date: '2024-10-25'
slug: chi-square-prop-testing
categories:
  - tidyverse
tags:
  - tidyverse
subtitle: ''
summary: 'Functions to run significance testing across lists and dataframes, then putting the results into a gt table'
authors: []
lastmod: '2024-10-25T11:07:37-07:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

{{% toc %}}

I’ve played with running the `prop.test()` chi-square tests across tibbles and I think I’ve finally got an efficient way of doing it.

## Simple prop testing

First, let’s look at a simple example. I have survey data from a control/exposed lift study and I want to see if the lift for each question is significant. The results are in a named list with a series of tidy tibbles.

``` r
result_list[1:3]
```

    ## $`I fully trust`
    ## # A tibble: 2 × 5
    ##   matched_control_xmedia svy_q  proportion     n total
    ##   <chr>                  <chr>       <dbl> <dbl> <dbl>
    ## 1 control                Brand1      0.339   339   998
    ## 2 test                   Brand1      0.431  1029  2389
    ## 
    ## $`I can fully identify with`
    ## # A tibble: 2 × 5
    ##   matched_control_xmedia svy_q  proportion     n total
    ##   <chr>                  <chr>       <dbl> <dbl> <dbl>
    ## 1 control                Brand1      0.289   288   998
    ## 2 test                   Brand1      0.385   919  2389
    ## 
    ## $`I really like`
    ## # A tibble: 2 × 5
    ##   matched_control_xmedia svy_q  proportion     n total
    ##   <chr>                  <chr>       <dbl> <dbl> <dbl>
    ## 1 control                Brand1      0.388   387   998
    ## 2 test                   Brand1      0.479  1144  2389

Structured like this, I can easily just use `pivot_wider()` to spread the data and then run the `prop.test()` function. I’ll write a function to do that .

``` r
result_prop_test <- function(res) {
  tmpout <- res |> 
    tidyr::pivot_wider(
      names_from = !!rlang::sym(names(res)[1]), 
      values_from = c(proportion, n, total)
    ) |> 
    dplyr::filter(!is.na(proportion_control)) |> 
    dplyr::filter(!is.na(proportion_test)) |> 
    dplyr::rowwise() |> 
    dplyr::mutate(
      prop_test = list(prop.test(c(n_control, n_test), c(total_control, total_test))),
      p_value = prop_test$p.value,  # Extract the p-value
      statistic = prop_test$statistic # Extract the chi-squared statistic
    ) |> 
    dplyr::mutate(lift = round((proportion_test - proportion_control)*100), 
                  sig_level = dplyr::case_when(p_value <= .05 ~ .95,
                                 p_value <= .1 & p_value >.05 ~ .90,
                                 p_value < .2 & p_value > .1 ~ .80)) 
  return(tmpout)
}
```

Now if we take a single tibble from our list and run it through our function, we can see that the results are now in one line and the p_value and statistic of the prop.test are in their own separate columns. The full results of the prop.test are also in a list column.

``` r
res1 <- result_prop_test(result_list[[1]])
res1
```

    ## # A tibble: 1 × 12
    ## # Rowwise: 
    ##   svy_q  proportion_control proportion_test n_control n_test total_control
    ##   <chr>               <dbl>           <dbl>     <dbl>  <dbl>         <dbl>
    ## 1 Brand1              0.339           0.431       339   1029           998
    ## # ℹ 6 more variables: total_test <dbl>, prop_test <list>, p_value <dbl>,
    ## #   statistic <dbl>, lift <dbl>, sig_level <dbl>

I can also just run the entire list through the function. It returns the same list format, but I can use `data.table::rbindlist()` to put into a tibble.

``` r
sig_results <- purrr::map(result_list, result_prop_test)

data.table::rbindlist(sig_results, idcol = "question") |> 
  as_tibble() |> 
  select(question, svy_q, contains("proportion"), lift, sig_level)
```

    ## # A tibble: 9 × 6
    ##   question              svy_q proportion_control proportion_test  lift sig_level
    ##   <chr>                 <chr>              <dbl>           <dbl> <dbl>     <dbl>
    ## 1 I fully trust         Bran…              0.339           0.431     9      0.95
    ## 2 I can fully identify… Bran…              0.289           0.385    10      0.95
    ## 3 I really like         Bran…              0.388           0.479     9      0.95
    ## 4 I would like to own   Bran…              0.395           0.487     9      0.95
    ## 5 I would be willing t… Bran…              0.262           0.383    12      0.95
    ## 6 Is leading in electr… Bran…              0.187           0.246     6      0.95
    ## 7 Is leading in digita… Bran…              0.297           0.388     9      0.95
    ## 8 Is leading in sustai… Bran…              0.231           0.319     9      0.95
    ## 9 Is a brand that will… Bran…              0.503           0.564     6      0.95

## Prop testing across all options

Now for something a little more involved. In the same survey that gave us the control/exposed, we also have questions asked for the entire competitive set. The data is currently in a tidy frame that looks like this:

``` r
head(df, 10)
```

    ## # A tibble: 10 × 6
    ##    Category           svy_q  proportion     n total cat          
    ##    <fct>              <chr>       <dbl> <dbl> <dbl> <chr>        
    ##  1 Aided Awareness    Brand1      0.959  3427  3574 Brand Metrics
    ##  2 Aided Awareness    Brand2      0.931  3329  3574 Brand Metrics
    ##  3 Aided Awareness    Brand3      0.909  3250  3574 Brand Metrics
    ##  4 Aided Awareness    Brand4      0.935  3340  3574 Brand Metrics
    ##  5 Aided Awareness    Brand5      0.936  3345  3574 Brand Metrics
    ##  6 Aided Ad Awareness Brand1      0.45   1543  3574 Brand Metrics
    ##  7 Aided Ad Awareness Brand2      0.392  1306  3574 Brand Metrics
    ##  8 Aided Ad Awareness Brand3      0.428  1392  3574 Brand Metrics
    ##  9 Aided Ad Awareness Brand4      0.408  1364  3574 Brand Metrics
    ## 10 Aided Ad Awareness Brand5      0.354  1185  3574 Brand Metrics

I want to compare the results for each Category question for each brand against all others. So the function below will do that for me. It also gives each brand an id and those id’s are then used to determine which brands have significantly greater results compared to the rest.

``` r
proptest_dataframe <- function(df, significance_level = 0.1) {
  df <- df %>%
    dplyr::mutate(id = LETTERS[1:n()])  # Assign capital letters as IDs
  
  run_prop_test_significant <- function(i, j, df) {
    prop1 <- df$proportion[i]
    prop2 <- df$proportion[j]
    
    n1 <- df$n[i]
    n2 <- df$n[j]
    
    total1 <- df$total[i]
    total2 <- df$total[j]
    
    counts <- c(n1, n2)
    totals <- c(total1, total2)
    
    test_result <- prop.test(counts, totals)
    
    return(test_result$p.value < significance_level && prop1 > prop2)
  }
  
  df$greater_than <- NA
  
  for (i in 1:nrow(df)) {
    greater_ids <- c()
    for (j in 1:nrow(df)) {
      if (i != j && run_prop_test_significant(i, j, df)) {
        greater_ids <- c(greater_ids, df$id[j])
      }
    }
    df$greater_than[i] <- paste(greater_ids, collapse = ", ")
  }
  return(df)
}

proptest_dataframe(df[1:5,])
```

    ## # A tibble: 5 × 8
    ##   Category        svy_q  proportion     n total cat           id    greater_than
    ##   <fct>           <chr>       <dbl> <dbl> <dbl> <chr>         <chr> <chr>       
    ## 1 Aided Awareness Brand1      0.959  3427  3574 Brand Metrics A     "B, C, D, E"
    ## 2 Aided Awareness Brand2      0.931  3329  3574 Brand Metrics B     "C"         
    ## 3 Aided Awareness Brand3      0.909  3250  3574 Brand Metrics C     ""          
    ## 4 Aided Awareness Brand4      0.935  3340  3574 Brand Metrics D     "C"         
    ## 5 Aided Awareness Brand5      0.936  3345  3574 Brand Metrics E     "C"

So this works, but because my dataframe has multiple questions included, I can’t just pass the whole thing in. Every row will be treated as if it’s all part of one, big prop.test.

To fix this, I’m going to split my data by the Category question and then I’m going to run it through this function.

``` r
df_list <- split(df, df$Category)

results <- df_list |> 
    purrr::map(proptest_dataframe)

results
```

    ## $`Aided Ad Awareness`
    ## # A tibble: 5 × 8
    ##   Category           svy_q  proportion     n total cat        id    greater_than
    ##   <fct>              <chr>       <dbl> <dbl> <dbl> <chr>      <chr> <chr>       
    ## 1 Aided Ad Awareness Brand1      0.45   1543  3574 Brand Met… A     "B, C, D, E"
    ## 2 Aided Ad Awareness Brand2      0.392  1306  3574 Brand Met… B     "E"         
    ## 3 Aided Ad Awareness Brand3      0.428  1392  3574 Brand Met… C     "B, E"      
    ## 4 Aided Ad Awareness Brand4      0.408  1364  3574 Brand Met… D     "E"         
    ## 5 Aided Ad Awareness Brand5      0.354  1185  3574 Brand Met… E     ""          
    ## 
    ## $`Aided Awareness`
    ## # A tibble: 5 × 8
    ##   Category        svy_q  proportion     n total cat           id    greater_than
    ##   <fct>           <chr>       <dbl> <dbl> <dbl> <chr>         <chr> <chr>       
    ## 1 Aided Awareness Brand1      0.959  3427  3574 Brand Metrics A     "B, C, D, E"
    ## 2 Aided Awareness Brand2      0.931  3329  3574 Brand Metrics B     "C"         
    ## 3 Aided Awareness Brand3      0.909  3250  3574 Brand Metrics C     ""          
    ## 4 Aided Awareness Brand4      0.935  3340  3574 Brand Metrics D     "C"         
    ## 5 Aided Awareness Brand5      0.936  3345  3574 Brand Metrics E     "C"         
    ## 
    ## $`Brand Momentum - Top 2 Box`
    ## # A tibble: 5 × 8
    ##   Category                 svy_q proportion     n total cat   id    greater_than
    ##   <fct>                    <chr>      <dbl> <dbl> <dbl> <chr> <chr> <chr>       
    ## 1 Brand Momentum - Top 2 … Bran…      0.556  1905  3574 Bran… A     "D"         
    ## 2 Brand Momentum - Top 2 … Bran…      0.579  1926  3574 Bran… B     "C, D"      
    ## 3 Brand Momentum - Top 2 … Bran…      0.559  1817  3574 Bran… C     "A"         
    ## 4 Brand Momentum - Top 2 … Bran…      0.53   1770  3574 Bran… D     ""          
    ## 5 Brand Momentum - Top 2 … Bran…      0.594  1987  3574 Bran… E     "A, C, D"   
    ## 
    ## $`Purchase Consideration`
    ## # A tibble: 5 × 8
    ##   Category               svy_q  proportion     n total cat    id    greater_than
    ##   <fct>                  <chr>       <dbl> <dbl> <dbl> <chr>  <chr> <chr>       
    ## 1 Purchase Consideration Brand1      0.498  1706  3574 Brand… A     "D, E"      
    ## 2 Purchase Consideration Brand2      0.499  1662  3574 Brand… B     "D, E"      
    ## 3 Purchase Consideration Brand3      0.518  1682  3574 Brand… C     "D, E"      
    ## 4 Purchase Consideration Brand4      0.443  1480  3574 Brand… D     ""          
    ## 5 Purchase Consideration Brand5      0.434  1452  3574 Brand… E     ""

Again, everything works as it should. But I also want to present these results to someone, and right now, the results are in a list of tidy tibbles. I’m going to add to my current `proptest_dataframe()` function to widen the data and also to do the prep work necessary to put these results into a [gt](https://gt.rstudio.com/index.html) table.

``` r
proptest_dataframe <- function(df, significance_level = 0.1) {
  df <- df %>%
    dplyr::mutate(id = LETTERS[1:n()])  # Assign capital letters as IDs
  
  run_prop_test_significant <- function(i, j, df) {
    prop1 <- df$proportion[i]
    prop2 <- df$proportion[j]
    
    n1 <- df$n[i]
    n2 <- df$n[j]
    
    total1 <- df$total[i]
    total2 <- df$total[j]
    
    counts <- c(n1, n2)
    totals <- c(total1, total2)
    
    test_result <- prop.test(counts, totals)
    
    return(test_result$p.value < significance_level && prop1 > prop2)
  }
  
  df$greater_than <- NA
  
  for (i in 1:nrow(df)) {
    greater_ids <- c()
    for (j in 1:nrow(df)) {
      if (i != j && run_prop_test_significant(i, j, df)) {
        greater_ids <- c(greater_ids, df$id[j])
      }
    }
    df$greater_than[i] <- paste(greater_ids, collapse = ", ")
  }
  
  # here i'm going to subscript my `greater_than` to show significance
  df <- df |> 
    mutate(
      brand_id = glue("{svy_q} {id}"),  
      proportion_with_sub = ifelse(
        greater_than != "", 
        glue("{round(proportion * 100)}% <sub>({greater_than})</sub>"),  
        glue("{round(proportion * 100)}%")
      )
    )
  
  # widen the data
  df_wide <- df |> 
    dplyr::select(Category, cat, brand_id, proportion_with_sub) |> 
    tidyr::pivot_wider(
      names_from = brand_id, 
      values_from = proportion_with_sub,
      values_fn = list(proportion_with_sub = function(x) paste(x, collapse = ""))
    )
  
  return(df_wide)
}

df_list <- split(df, df$Category)

results <- df_list |> 
    purrr::map(proptest_dataframe)

results
```

    ## $`Aided Ad Awareness`
    ## # A tibble: 1 × 7
    ##   Category          cat   `Brand1 A` `Brand2 B` `Brand3 C` `Brand4 D` `Brand5 E`
    ##   <fct>             <chr> <chr>      <chr>      <chr>      <chr>      <chr>     
    ## 1 Aided Ad Awarene… Bran… 45% <sub>… 39% <sub>… 43% <sub>… 41% <sub>… 35%       
    ## 
    ## $`Aided Awareness`
    ## # A tibble: 1 × 7
    ##   Category        cat     `Brand1 A` `Brand2 B` `Brand3 C` `Brand4 D` `Brand5 E`
    ##   <fct>           <chr>   <chr>      <chr>      <chr>      <chr>      <chr>     
    ## 1 Aided Awareness Brand … 96% <sub>… 93% <sub>… 91%        94% <sub>… 94% <sub>…
    ## 
    ## $`Brand Momentum - Top 2 Box`
    ## # A tibble: 1 × 7
    ##   Category          cat   `Brand1 A` `Brand2 B` `Brand3 C` `Brand4 D` `Brand5 E`
    ##   <fct>             <chr> <chr>      <chr>      <chr>      <chr>      <chr>     
    ## 1 Brand Momentum -… Bran… 56% <sub>… 58% <sub>… 56% <sub>… 53%        59% <sub>…
    ## 
    ## $`Purchase Consideration`
    ## # A tibble: 1 × 7
    ##   Category          cat   `Brand1 A` `Brand2 B` `Brand3 C` `Brand4 D` `Brand5 E`
    ##   <fct>             <chr> <chr>      <chr>      <chr>      <chr>      <chr>     
    ## 1 Purchase Conside… Bran… 50% <sub>… 50% <sub>… 52% <sub>… 44%        43%

Note that the column names have a capital letter next to them; that way each brand is readily identifiable for the reader of the table. Now we’re going to bind our data into a single tibble, and then we’re going to pass it to gt to make a table.

``` r
combined_result <- results |> 
    data.table::rbindlist(fill = TRUE) |> 
    tibble::as_tibble() |> 
    dplyr::mutate(Category = as.character(Category))

library(gt)
  
  # Programmatically generate the labels for the columns (with <br>)
  col_labels <- combined_result |> 
    colnames() |> 
    set_names() |>   # Keeps the original column names as-is
    map(~ html(glue("{gsub(' ', '<br>', .)}")))  # Programmatically replace space with <br> in labels
  
  # Create the gt table with programmatically generated column labels and subscripting
  combined_result |> 
    group_by(cat) |> 
    gt() |> 
    tab_header(
      title = "Competitive summary",
      ) |> 
    cols_label(.list = col_labels) |> 
    fmt_markdown(columns = everything()) |> 
    # bolding groups and columns 
    tab_style(
      style = cell_text(weight = 'bold',
                        size = px(13)),
      locations = list(
        cells_column_labels(
          columns = everything()),
        cells_row_groups(groups = TRUE))
    ) |> 
    gt::tab_footnote(
      footnote = "A footnote goes here to explain the table",
      locations = gt::cells_title(groups = "title")
    ) |> 
    # aligning the title left
    gt::tab_style(
      style = gt::cell_text(align = 'left',
                            weight = 'bold',
                            size = gt::px(16)),
      locations = gt::cells_title(c("title"))
    ) |> 
    # aligning the subtitle left
    gt::tab_style(
      style = gt::cell_text(align = 'left',
                            weight = 'bold',
                            size = gt::px(14)),
      locations = gt::cells_title(c("subtitle")) 
    ) |> 
    # centering column labels (not first column)
    gt::tab_style(
      style = gt::cell_text(align = 'center'),
      locations = gt::cells_column_labels(
        columns = -1  # Exclude the first column
      )
    ) |>
    # font size in table
    gt::tab_style(
      style = gt::cell_text(size = px(12)),
      locations = cells_body(
        columns = dplyr::everything())
    ) |> 
    # centering all variables in table (not first column)
    gt::tab_style(
      style = gt::cell_text(align = "center"),
      locations = gt::cells_body(columns = -1)
    ) |> 
    # don't have Category column labeled
    cols_label(
      Category = ""
    ) |> 
    # final options
    gt::tab_options(
      data_row.padding = gt::px(4),
      row_group.padding = gt::px(4),
      source_notes.font.size = gt::px(10),
      footnotes.font.size = gt::px(10),
      footnotes.marks = "" # empty footnote mark
    ) 
```

<div id="prjjjqrtwe" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#prjjjqrtwe table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
&#10;#prjjjqrtwe thead, #prjjjqrtwe tbody, #prjjjqrtwe tfoot, #prjjjqrtwe tr, #prjjjqrtwe td, #prjjjqrtwe th {
  border-style: none;
}
&#10;#prjjjqrtwe p {
  margin: 0;
  padding: 0;
}
&#10;#prjjjqrtwe .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
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
&#10;#prjjjqrtwe .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#prjjjqrtwe .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}
&#10;#prjjjqrtwe .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#prjjjqrtwe .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#prjjjqrtwe .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#prjjjqrtwe .gt_col_headings {
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
&#10;#prjjjqrtwe .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
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
&#10;#prjjjqrtwe .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}
&#10;#prjjjqrtwe .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#prjjjqrtwe .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#prjjjqrtwe .gt_column_spanner {
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
&#10;#prjjjqrtwe .gt_spanner_row {
  border-bottom-style: hidden;
}
&#10;#prjjjqrtwe .gt_group_heading {
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
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
&#10;#prjjjqrtwe .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
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
&#10;#prjjjqrtwe .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#prjjjqrtwe .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#prjjjqrtwe .gt_row {
  padding-top: 4px;
  padding-bottom: 4px;
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
&#10;#prjjjqrtwe .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#prjjjqrtwe .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
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
&#10;#prjjjqrtwe .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#prjjjqrtwe .gt_row_group_first th {
  border-top-width: 2px;
}
&#10;#prjjjqrtwe .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#prjjjqrtwe .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#prjjjqrtwe .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#prjjjqrtwe .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#prjjjqrtwe .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#prjjjqrtwe .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#prjjjqrtwe .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}
&#10;#prjjjqrtwe .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#prjjjqrtwe .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#prjjjqrtwe .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
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
&#10;#prjjjqrtwe .gt_footnote {
  margin: 0px;
  font-size: 10px;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#prjjjqrtwe .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
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
&#10;#prjjjqrtwe .gt_sourcenote {
  font-size: 10px;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#prjjjqrtwe .gt_left {
  text-align: left;
}
&#10;#prjjjqrtwe .gt_center {
  text-align: center;
}
&#10;#prjjjqrtwe .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#prjjjqrtwe .gt_font_normal {
  font-weight: normal;
}
&#10;#prjjjqrtwe .gt_font_bold {
  font-weight: bold;
}
&#10;#prjjjqrtwe .gt_font_italic {
  font-style: italic;
}
&#10;#prjjjqrtwe .gt_super {
  font-size: 65%;
}
&#10;#prjjjqrtwe .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}
&#10;#prjjjqrtwe .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#prjjjqrtwe .gt_indent_1 {
  text-indent: 5px;
}
&#10;#prjjjqrtwe .gt_indent_2 {
  text-indent: 10px;
}
&#10;#prjjjqrtwe .gt_indent_3 {
  text-indent: 15px;
}
&#10;#prjjjqrtwe .gt_indent_4 {
  text-indent: 20px;
}
&#10;#prjjjqrtwe .gt_indent_5 {
  text-indent: 25px;
}
&#10;#prjjjqrtwe .katex-display {
  display: inline-flex !important;
  margin-bottom: 0.75em !important;
}
&#10;#prjjjqrtwe div.Reactable > div.rt-table > div.rt-thead > div.rt-tr.rt-tr-group-header > div.rt-th-group:after {
  height: 0px !important;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_heading">
      <td colspan="6" class="gt_heading gt_title gt_font_normal gt_bottom_border" style="font-size: 16px; text-align: left; font-weight: bold;">Competitive summary<span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height: 0;"><sup></sup></span></td>
    </tr>
    &#10;    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold;" scope="col" id=""></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="Brand1&lt;br&gt;A">Brand1<br>A</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="Brand2&lt;br&gt;B">Brand2<br>B</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="Brand3&lt;br&gt;C">Brand3<br>C</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="Brand4&lt;br&gt;D">Brand4<br>D</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" style="font-size: 13px; font-weight: bold; text-align: center;" scope="col" id="Brand5&lt;br&gt;E">Brand5<br>E</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr class="gt_group_heading_row">
      <th colspan="6" class="gt_group_heading" style="font-size: 13px; font-weight: bold;" scope="colgroup" id="Brand Metrics">Brand Metrics</th>
    </tr>
    <tr class="gt_row_group_first"><td headers="Brand Metrics  Category" class="gt_row gt_left" style="font-size: 12px;"><span class='gt_from_md'>Aided Ad Awareness</span></td>
<td headers="Brand Metrics  Brand1 A" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>45% <sub>(B, C, D, E)</sub></span></td>
<td headers="Brand Metrics  Brand2 B" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>39% <sub>(E)</sub></span></td>
<td headers="Brand Metrics  Brand3 C" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>43% <sub>(B, E)</sub></span></td>
<td headers="Brand Metrics  Brand4 D" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>41% <sub>(E)</sub></span></td>
<td headers="Brand Metrics  Brand5 E" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>35%</span></td></tr>
    <tr><td headers="Brand Metrics  Category" class="gt_row gt_left" style="font-size: 12px;"><span class='gt_from_md'>Aided Awareness</span></td>
<td headers="Brand Metrics  Brand1 A" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>96% <sub>(B, C, D, E)</sub></span></td>
<td headers="Brand Metrics  Brand2 B" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>93% <sub>(C)</sub></span></td>
<td headers="Brand Metrics  Brand3 C" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>91%</span></td>
<td headers="Brand Metrics  Brand4 D" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>94% <sub>(C)</sub></span></td>
<td headers="Brand Metrics  Brand5 E" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>94% <sub>(C)</sub></span></td></tr>
    <tr><td headers="Brand Metrics  Category" class="gt_row gt_left" style="font-size: 12px;"><span class='gt_from_md'>Brand Momentum - Top 2 Box</span></td>
<td headers="Brand Metrics  Brand1 A" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>56% <sub>(D)</sub></span></td>
<td headers="Brand Metrics  Brand2 B" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>58% <sub>(C, D)</sub></span></td>
<td headers="Brand Metrics  Brand3 C" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>56% <sub>(A)</sub></span></td>
<td headers="Brand Metrics  Brand4 D" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>53%</span></td>
<td headers="Brand Metrics  Brand5 E" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>59% <sub>(A, C, D)</sub></span></td></tr>
    <tr><td headers="Brand Metrics  Category" class="gt_row gt_left" style="font-size: 12px;"><span class='gt_from_md'>Purchase Consideration</span></td>
<td headers="Brand Metrics  Brand1 A" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>50% <sub>(D, E)</sub></span></td>
<td headers="Brand Metrics  Brand2 B" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>50% <sub>(D, E)</sub></span></td>
<td headers="Brand Metrics  Brand3 C" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>52% <sub>(D, E)</sub></span></td>
<td headers="Brand Metrics  Brand4 D" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>44%</span></td>
<td headers="Brand Metrics  Brand5 E" class="gt_row gt_left" style="font-size: 12px; text-align: center;"><span class='gt_from_md'>43%</span></td></tr>
  </tbody>
  &#10;  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="6"><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height: 0;"><sup></sup></span> A footnote goes here to explain the table</td>
    </tr>
  </tfoot>
</table>
</div>
