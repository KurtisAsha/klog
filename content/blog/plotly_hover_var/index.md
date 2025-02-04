---
title: "Alternative `plotly` Hover Text In Custom Function"
author: "Author: Kurtis Smith"
date: "2024-10-22"
slug: plotly-hover-var
#bibliography: [references.bib]
output: 
 html_document:
  fig_width: 10
  fig_height: 6
categories:
  - self-tech-support
tags:
  - plotly
  - rlang
  - rstats
summary: "How to add alternative text when hovering over a value using `plotly` for R."
---

<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<script src="{{< blogdown/postref >}}index_files/plotly-binding/plotly.js"></script>
<script src="{{< blogdown/postref >}}index_files/typedarray/typedarray.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/plotly-htmlwidgets-css/plotly-htmlwidgets.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/plotly-main/plotly-latest.min.js"></script>

## Problem

How to add alternative text when hovering over a value using `plotly` for R.

A few considerations I had to navigate:

- Plotly behaves well when referencing columns already assigned in one of it’s arguments, for this problem, the variable in question was in the assigned data but not assigned to any argument. In the example below, this would be the “alt_text” column

- The alternative text was to be passed into a custom function

- I found a workaround ([Thanks DJack from Stack Overflow](https://stackoverflow.com/posts/49902288/revisions)) which subsets data using \$ but I needed to be able to pass any column through custom function

Let’s begin with creating test data.

``` r
# Load packages
library(plotly)
library(rlang)
library(patchwork)

# Create data
df <- data.frame(
 x = 1,
 y = 1,
 text = "text",
 alt_text = "alt_text"
)
```

## Solution

TEXT

``` r
create_plot <- function(df, text){
 
 text <- enquo(text)

  plot_ly(
  data = df, 
  x = ~x, 
  y = ~y, 
  type = "scatter",
  mode = "text+marker",
  text = text,
  textfont = list(size = 40)
  )
 
}

create_plot_alt <- function(df, text, alt_text){
 
 text <- enquo(text)
 alt_text <- enquo(alt_text)
 
 plot_ly(
  data = df,
  x = ~x, 
  y = ~y, 
  type = "scatter",
  mode = "text+marker",
  text = text,
  textfont = list(size = 40),
  hovertemplate = df[[rlang::quo_get_expr(alt_text)]] 
  )
 
}
```

It works! The left shows how it looked using

``` r
subplot(
 create_plot(df = df, text = text),
 create_plot_alt(df = df, text = text, alt_text = alt_text)
)
```

<div class="plotly html-widget html-fill-item" id="htmlwidget-1" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"data":[{"x":[1],"y":[1],"mode":"text+marker","text":"text","textfont":{"size":40},"type":"scatter","marker":{"color":"rgba(31,119,180,1)","line":{"color":"rgba(31,119,180,1)"}},"error_y":{"color":"rgba(31,119,180,1)"},"error_x":{"color":"rgba(31,119,180,1)"},"line":{"color":"rgba(31,119,180,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[1],"y":[1],"mode":"text+marker","text":"text","textfont":{"size":40},"hovertemplate":"alt_text","type":"scatter","marker":{"color":"rgba(255,127,14,1)","line":{"color":"rgba(255,127,14,1)"}},"error_y":{"color":"rgba(255,127,14,1)"},"error_x":{"color":"rgba(255,127,14,1)"},"line":{"color":"rgba(255,127,14,1)"},"xaxis":"x2","yaxis":"y2","frame":null}],"layout":{"xaxis":{"domain":[0,0.47999999999999998],"automargin":true,"anchor":"y"},"xaxis2":{"domain":[0.52000000000000002,1],"automargin":true,"anchor":"y2"},"yaxis2":{"domain":[0,1],"automargin":true,"anchor":"x2"},"yaxis":{"domain":[0,1],"automargin":true,"anchor":"x"},"annotations":[],"shapes":[],"images":[],"margin":{"b":40,"l":60,"t":25,"r":10},"hovermode":"closest","showlegend":true},"attrs":{"2a647cda3d42":{"x":{},"y":{},"mode":"text+marker","text":{},"textfont":{"size":40},"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"},"2a641db13a19":{"x":{},"y":{},"mode":"text+marker","text":{},"textfont":{"size":40},"hovertemplate":"alt_text","alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"subplot":true,"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

# Conclusion

## Acknowledgements

Packages and package maintainer(s):

- slider \| Davis Vaughan
- tidyverse \| Hadley Wickham
