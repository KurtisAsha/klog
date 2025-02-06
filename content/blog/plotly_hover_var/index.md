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

This **create_plot_alt()** function demonstrates the difference in syntax. Inspired by Stack Overflows Djack but amended to suit a custom function, `rlang::quo_get_expr()` with \[\[\]\] allows for referencing additional columns.

**It works!**. The left plot on hover will show “text”, whilst the right shows “alt_text”

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

subplot(
 # same hover text as text plotted
 style(p1, showlegend = FALSE),
 # alternative hover text
 style(p_alt, showlegend = FALSE) 
)
```

<div id="htmlwidget-1" style="width:700px;height:400px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"data":[{"x":[1],"y":[1],"mode":"text+marker","text":"text","hovertemplate":"text","textfont":{"size":50,"color":"#b44046"},"type":"scatter","marker":{"color":"rgba(31,119,180,1)","line":{"color":"rgba(31,119,180,1)"}},"error_y":{"color":"rgba(31,119,180,1)"},"error_x":{"color":"rgba(31,119,180,1)"},"line":{"color":"rgba(31,119,180,1)"},"xaxis":"x","yaxis":"y","frame":null,"showlegend":false},{"x":[1],"y":[1],"mode":"text+marker","text":"text","textfont":{"size":50,"color":"#57a2a4"},"hovertemplate":"alt_text","type":"scatter","marker":{"color":"rgba(255,127,14,1)","line":{"color":"rgba(255,127,14,1)"}},"error_y":{"color":"rgba(255,127,14,1)"},"error_x":{"color":"rgba(255,127,14,1)"},"line":{"color":"rgba(255,127,14,1)"},"xaxis":"x2","yaxis":"y2","frame":null,"showlegend":false}],"layout":{"xaxis":{"domain":[0,0.47999999999999998],"automargin":true,"zeroline":false,"showline":false,"showticklabels":false,"anchor":"y"},"xaxis2":{"domain":[0.52000000000000002,1],"automargin":true,"zeroline":false,"showline":false,"showticklabels":false,"anchor":"y2"},"yaxis2":{"domain":[0,1],"automargin":true,"zeroline":false,"showline":false,"showticklabels":false,"anchor":"x2"},"yaxis":{"domain":[0,1],"automargin":true,"zeroline":false,"showline":false,"showticklabels":false,"anchor":"x"},"annotations":[],"shapes":[],"images":[],"width":700,"height":400,"margin":{"b":40,"l":60,"t":25,"r":10},"hovermode":"closest","showlegend":false},"attrs":{"1b8473d4307a":{"x":{},"y":{},"mode":"text+marker","text":{},"hovertemplate":{},"textfont":{"size":50,"color":"#b44046"},"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"},"1b8478365827":{"x":{},"y":{},"mode":"text+marker","text":{},"textfont":{"size":50,"color":"#57a2a4"},"hovertemplate":"alt_text","alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"subplot":true,"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

# Conclusion

Getting this to work took me too long to admit and later became more complicated by passing a SharedData R6 class object for use with the `crosstalk` package. I’ll leave that battle for another day. Taking wins where I can, I’ll bank this fix and look to explore `rlang` in more detail in the future.

## Acknowledgements

Packages and package maintainer(s):

- plotly \| Carson Sievert
- rlang \| Lionel Henry
