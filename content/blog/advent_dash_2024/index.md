---
title: "Advent Calendar Ratings Dashboard"
author: "Author: Kurtis Smith"
date: "2025-01-27"
slug: advent-dash-fm
#bibliography: [references.bib]
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
---


```r
# Load packages
library(tidyverse)
library(janitor)

theme_set(theme_minimal(
 base_family = 'serif', base_size = 12))

advent_appraisals_2024 <- read_csv("./input/advent-appraisal.csv")  %>% 
 clean_names()
```

## Project Introduction

For a bit of festive fun, I built a flexdashboard from Fortnum & Mason's advert calendar ratings. Participants included:
-   Sofia Costa, resident wordsmith
-   Laura da Costa Williams, resident chef, with green fingers
-   Skye da Costa Williams, resident witch, with crotchet hooks
-   Me, resident R-nerd

[Fortnum & Mason's](https://www.fortnumandmason.com/) 2024 advent calendar for two, consisted of two biscuits and two accompanying teas per day. We all rated the biscuit, tea, and the pairing. 

[Advent Dashboard](https://rawcdn.githack.com/KurtisAsha/annual-advent-appraisal/0bf4b0f4813e3eebc95b12006af518bcf92cbdb8/advent-appraisal-2024.html)

# Data




# Conclusion



## Acknowledgements

Packages and package maintainer(s):

-   janitor \| Sam Firke
-   tidyverse \| Hadley Wickham
