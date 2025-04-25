---
title: "Advent Calendar Ratings Dashboard"
author: "Author: Kurtis Smith"
date: "2025-01-28"
slug: advent-dash-fm
output: 
 html_document:
  fig_width: 10
  fig_height: 6
categories:
  - fun
tags:
  - flexdashboard
  - rstats
summary: "A bit of festive fun: flexdashboard from Fortnum & Mason's advert calendar ratings"
editor_options: 
  markdown: 
    wrap: 72
---

<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>

## Project Introduction

For a bit of festive fun 🎄, I built a flexdashboard from Fortnum & Mason’s
advert calendar ratings. Participants included:

- Sofia Costa, resident wordsmith

- Laura da Costa Williams, resident chef, with [green
  fingers](https://www.instagram.com/laura_surbangarden/)

- Skye da Costa Williams, resident witch, with [crotchet
  hooks](https://www.instagram.com/skyescrochet/)

- Me, resident R-nerd

[Fortnum & Mason](https://www.fortnumandmason.com/) was established in
1707, during that time it has been known for many things, including
specialising in tin goods and becoming a post office. Today it’s known
for afternoon tea and food hampers. Their 2024 advent calendar for two
consisted of two biscuits and two accompanying teas per day.

## Data

We all rated the biscuit, tea, and the pairing out of 10, recording our
results in a dependable spreadsheet. After which I ingested and after a
**few steps** created a flexdashboard. You can check these steps in this
[repo](https://github.com/KurtisAsha/annual-advent-appraisal).

An idea of the data

<div class="datatables html-widget html-fill-item" id="htmlwidget-1" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"filter":"none","vertical":false,"data":[[1,1,1,1],["Royal Blend","Royal Blend","Royal Blend","Royal Blend"],["Chocolossus","Chocolossus","Chocolossus","Chocolossus"],["Sofia","Kurtis","Skye","Laura"],[10,9,10,9],[4,8,6,9.5],[1,2,7,7]],"container":"<table class=\"compact hover row-border\">\n  <thead>\n    <tr>\n      <th>day<\/th>\n      <th>tea_name<\/th>\n      <th>biscuit_name<\/th>\n      <th>reviewer<\/th>\n      <th>tea_score_out_of_10<\/th>\n      <th>biscuit_score_out_of_10<\/th>\n      <th>combination_score_out_of_10<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"dom":"t","columnDefs":[{"className":"dt-right","targets":[0,4,5,6]},{"name":"day","targets":0},{"name":"tea_name","targets":1},{"name":"biscuit_name","targets":2},{"name":"reviewer","targets":3},{"name":"tea_score_out_of_10","targets":4},{"name":"biscuit_score_out_of_10","targets":5},{"name":"combination_score_out_of_10","targets":6}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

## Dashboard

A few navigation notes:

- There are three tabs at the top: Tea, Biscuit, and Pairing

- Each tab has:

  - 3 static value boxes: Best name, Best score, Average score

  - An interactive line chart with hover functionality and click /
    doubleclick legend values

  - An interactive bar chart, best used by hovering over a row and
    comparing values. The lower the value the closer that person
    scored. This was used to determine who had similar taste buds.

> Here is the [**Advent
> Dashboard**](https://rawcdn.githack.com/KurtisAsha/annual-advent-appraisal/0bf4b0f4813e3eebc95b12006af518bcf92cbdb8/advent-appraisal-2024.html)

## Notes

There was an issue of same scoring Tea, Biscuit, or pairing. The
flexdashboard would push all other boxes out to accommodate multiple
values. Luckily, in the end there was non.

It was a kind-of rush job, I wanted to show the dashboard close to finishing the advent calendars.

I showed the dashboard to all participants, were they interested? Well
for 10 minutes until Christmas dinner was mentioned. Who can blame them
🤤.

## Acknowledgements

Participants for going along with this!

Packages and package maintainer(s):

- janitor \| Sam Firke
- tidyverse \| Hadley Wickham
- plotly \| Carson Sievert
- DT \| Joe Cheng
