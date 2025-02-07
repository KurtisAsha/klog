---
title: "Alternative Hover Text In Custom Function with  `plotly`"
author: "Author: Kurtis Smith"
date: "2025-02-06"
slug: plotly-hover-var
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

- `plotly` behaves well when referencing columns already assigned in one of it’s arguments, for this problem, the variable in question was in the assigned data but not assigned to any argument. In the example below, this would be the “alt_text” column

- The alternative text was to be passed into a custom function

- I found a workaround ([Thanks DJack from Stack Overflow](https://stackoverflow.com/posts/49902288/revisions)) which subsets data using \$ but I needed to be able to pass any column through a custom function

Let’s begin with creating test data.

``` r
# Load packages
library(plotly)
library(rlang)

# Create data
df <- data.frame(
 x = 1,
 y = 1,
 text = "text",
 alt_text = "alt_text"
)
```

## Solution

This **create_plot()** function demonstrates how referencing a column against an argument, allows easy access for the hovertemplate argument to be assigned one of those referenced columns. In this example - text.

``` r
create_plot <- function(df, text){
 
 text <- enquo(text)

  plot_ly(
  data = df, 
  x = ~x, 
  y = ~y, 
  width = 300, 
  height = 300,
  type = "scatter",
  mode = "text+marker",
  text = text,
  # not necessary but demonstrates code differences
  hovertemplate = text,
  textfont = list(size = 50, color = "#b44046"))}
```

This **create_plot_alt()** function demonstrates the difference in syntax. Inspired by Stack Overflows Djack but amended to suit a custom function, `rlang::quo_get_expr()` with \[\[\]\] allows for referencing additional columns.

``` r
create_plot_alt <- function(df, text, alt_text){
 
 text <- enquo(text)
 alt_text <- enquo(alt_text)
 
 plot_ly(
  data = df,
  x = ~x, 
  y = ~y, 
  width = 300, 
  height = 300,
  type = "scatter",
  mode = "text+marker",
  text = text,
  textfont = list(size = 50, color = "#57a2a4"),
  hovertemplate = df[[rlang::quo_get_expr(alt_text)]] 
  )
 
}
```

**It works!**. The first plot with red text - on hover will show “text”, whilst the second plot with blue text shows “alt_text”

``` r
# To remove elements not needed
void <- list(
  title = "",
  zeroline = FALSE,
  showline = FALSE,
  showticklabels = FALSE
)

p1 <- create_plot(df = df, text = text) %>% 
 layout(xaxis = void, yaxis = void)

p_alt <- create_plot_alt(df = df, text = text, alt_text = alt_text) %>% 
 layout(xaxis = void, yaxis = void)

# same hover text as text plotted
 style(p1, showlegend = FALSE)
```

<div id="htmlwidget-1" style="width:300px;height:300px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"visdat":{"2eb01e4d7b02":["function () ","plotlyVisDat"]},"cur_data":"2eb01e4d7b02","attrs":{"2eb01e4d7b02":{"x":{},"y":{},"mode":"text+marker","text":{},"hovertemplate":{},"textfont":{"size":50,"color":"#b44046"},"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"width":300,"height":300,"margin":{"b":40,"l":60,"t":25,"r":10},"xaxis":{"domain":[0,1],"automargin":true,"title":"","zeroline":false,"showline":false,"showticklabels":false},"yaxis":{"domain":[0,1],"automargin":true,"title":"","zeroline":false,"showline":false,"showticklabels":false},"hovermode":"closest","showlegend":false},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[1],"y":[1],"mode":"text+marker","text":"text","hovertemplate":"text","textfont":{"size":50,"color":"#b44046"},"type":"scatter","marker":{"color":"rgba(31,119,180,1)","line":{"color":"rgba(31,119,180,1)"}},"error_y":{"color":"rgba(31,119,180,1)"},"error_x":{"color":"rgba(31,119,180,1)"},"line":{"color":"rgba(31,119,180,1)"},"xaxis":"x","yaxis":"y","frame":null,"showlegend":false}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

``` r
# alternative hover text
 style(p_alt, showlegend = FALSE) 
```

<div id="htmlwidget-2" style="width:300px;height:300px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-2">{"x":{"visdat":{"2eb0ab41a9b":["function () ","plotlyVisDat"]},"cur_data":"2eb0ab41a9b","attrs":{"2eb0ab41a9b":{"x":{},"y":{},"mode":"text+marker","text":{},"textfont":{"size":50,"color":"#57a2a4"},"hovertemplate":"alt_text","alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"width":300,"height":300,"margin":{"b":40,"l":60,"t":25,"r":10},"xaxis":{"domain":[0,1],"automargin":true,"title":"","zeroline":false,"showline":false,"showticklabels":false},"yaxis":{"domain":[0,1],"automargin":true,"title":"","zeroline":false,"showline":false,"showticklabels":false},"hovermode":"closest","showlegend":false},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[1],"y":[1],"mode":"text+marker","text":"text","textfont":{"size":50,"color":"#57a2a4"},"hovertemplate":"alt_text","type":"scatter","marker":{"color":"rgba(31,119,180,1)","line":{"color":"rgba(31,119,180,1)"}},"error_y":{"color":"rgba(31,119,180,1)"},"error_x":{"color":"rgba(31,119,180,1)"},"line":{"color":"rgba(31,119,180,1)"},"xaxis":"x","yaxis":"y","frame":null,"showlegend":false}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

# Conclusion

Getting this to work took me too long to admit and later became more complicated by passing a SharedData R6 class object for use with the `crosstalk` package. I’ll leave that battle for another day. Taking wins where I can, I’ll bank this fix and look to explore `rlang` in more detail in the future.

## Acknowledgements

Packages and package maintainer(s):

- plotly \| Carson Sievert
- rlang \| Lionel Henry
