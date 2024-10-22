---
title: "Rolling 5-year Sum Label Using `slider`"
author: "Author: Kurtis Smith"
date: "2024-10-22"
slug: rolling-sum-label
#bibliography: [references.bib]
output: 
 html_document:
  fig_width: 10
  fig_height: 6
categories:
  - self-tech-support
tags:
  - slider
  - rstats
summary: "Finding a way to label a rolling 5-year sum for plotting using the `slider` package."
---

## Problem

First blog post off the back of an issue at work which required desktop research to figure out. This content aims to better cement my learning and to share to anyone who has been afflicted with a similar issue. 

For context, I was unable to download new packages and only had the `slider` package available. Notable packages which came up in research are `zoo` and `TTR` which could provide similar results I'm sure, but the focus here is on `slider`. 

Whilst calculating a rolling sum took a little while to figure out, the greater block was generating a label for plotting. 

Let's begin with setting global options and getting data.


```r
# Load packages
library(tidyverse)
library(slider)

theme_set(theme_minimal(
 base_family = 'serif', base_size = 12))

set.seed(4321)

# Get data
example_df <- bind_rows(
 tibble(group = "group_1",
  year = c(1991:1993, 1995, 2000:2005),
  to_add = sample(1:40, 10)),
 
 tibble(group = "group_2",
  year = c(2000:2005),
  to_add = sample(1:40, 6)))
```

## Calculate Rolling Sum

Calculate the rolling sum using an reference column or index, in this instance year. Set before argument to 4 which will then include the value itself to provide a rolling 5 year window. This code is using the `_int` variant of the `slide_index_*` functions for integer but there are equivalents for double, logical, character, and data frame. 

In the function(s) `slide_index_*` you can't reference the column that's being iterated on. Instead I used the `slide` function to generate labels for plotting. 


```r
example_df %>%
 group_by(group) %>% 
 arrange(year) %>%
 mutate(
  # Calculate rolling sum
  rolling_5_years = slide_index_int(to_add, year, sum, .before = 4),
  # Generate rolling sum labels
  rolling_5_years_label = slide(year, ~.x, .before = 4) %>%
   as.character() %>%
   unlist()
 ) %>% 
 head(5)
```

```
## # A tibble: 5 × 5
## # Groups:   group [1]
##   group    year to_add rolling_5_years rolling_5_years_label          
##   <chr>   <dbl>  <int>           <int> <chr>                          
## 1 group_1  1991     27              27 1991                           
## 2 group_1  1992     29              56 c(1991, 1992)                  
## 3 group_1  1993     10              66 c(1991, 1992, 1993)            
## 4 group_1  1995     15              81 c(1991, 1992, 1993, 1995)      
## 5 group_1  2000     24              24 c(1991, 1992, 1993, 1995, 2000)
```

Note that this function helpfully counts missing values within the sequence. See above example, 1994 is missing but for 1995 the rolling years sum of 65 includes only 1991, 1992, 1993, and 1995 `to_add` values.

Now to visualise!

## Visualise

Important to note when using `slide` to make a label, when the groups starting index value mismatch you will get the below plot which does look messy.


```r
example_df %>%
 group_by(group) %>% 
 arrange(year) %>%
 mutate(
  # Calculate rolling sum
  rolling_5_years = slide_index_int(to_add, year, sum, .before = 4),
  # Generate rolling sum labels
  rolling_5_years_label = slide(year, ~.x, .before = 4) %>%
   as.character() %>%
   unlist()
 ) %>% 
 ggplot(aes(x = rolling_5_years, 
            y = rolling_5_years_label, 
            fill = group)) +
 geom_col(position = "dodge", colour = "white") +
 labs(title = "Rolling 5 year period example",
      subtitle = "A little messy", 
      x = "", y = "") +
 scale_fill_viridis_d(alpha = 0.75, name = "") +
 theme(
  legend.position = "top",
  plot.title.position = "plot"
 ) 
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="672" />

You may be required to include mismatched starting index values for your use case. But if that's not the case and you want to provide a bit more clarity to the plot - I've filtered to exclude any year before 2000, now both groups start at the same year / the same starting index value.

Not all rolling years have the same number of years unless you add the `.complete = TRUE` argument. Be sure to filter out NA's afterwards.

I've coded the labels to collapse years into colon format to maximise plot space.


```r
# Set base rolling value the same
example_rolling_sum <- example_df %>%
 filter(!year < 2000) %>% 
 group_by(group) %>% 
 arrange(year) %>%
 mutate(
  # Calculate rolling sum
  rolling_5_years = slide_index_int(to_add, year, sum, .before = 4, .complete = TRUE),
  # Generate rolling sum labels
  rolling_5_years_label = slide(as.double(year), ~.x, .before = 4, .complete = TRUE) %>%
   map(~paste(min(.), ":", max(.))) %>% 
   unlist()
 )
```

```
## Warning: There were 16 warnings in `mutate()`.
## The first warning was:
## ℹ In argument: `rolling_5_years_label = `%>%`(...)`.
## ℹ In group 1: `group = "group_1"`.
## Caused by warning in `min()`:
## ! no non-missing arguments to min; returning Inf
## ℹ Run `dplyr::last_dplyr_warnings()` to see the 15 remaining warnings.
```

```r
 # Plot
 example_rolling_sum %>% 
 filter(!is.na(rolling_5_years)) %>% 
 ggplot(aes(x = rolling_5_years, 
            y = rolling_5_years_label, 
            fill = group)) +
 geom_col(position = "dodge", colour = "white") +
  labs(title = "Rolling 5 year period example", 
       subtitle = "Pretty",
       x = "", y = "") +
 scale_fill_viridis_d(alpha = 0.75, name = "") +
 theme(
  legend.position = "top",
  plot.title.position = "plot"
 ) 
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />

# Conclusion

I think `slider` is a great package, was I forced to use it because I couldn't download any other package - sure. But would I use it again given the keys to CRAN, yep. Hope this tidbit about labels helped in some small way. That brings me to the end of the first self-tech-support blog. 

## Acknowledgements

Packages and package maintainer(s):

-   slider \| Davis Vaughan
-   tidyverse \| Hadley Wickham
