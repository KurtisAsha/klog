---
title: "Shiny Assisted Solar Ages"
author: "Author: Kurtis Smith"
date: "2025-05-27"
slug: shiny-solar-ages
output: 
 html_document:
  fig_width: 10
  fig_height: 6
categories:
  - fun
tags:
  - shiny-assistant
  - rstats
summary: "Building a solar system age converter with support from Shiny Assistant"
editor_options: 
  markdown: 
    wrap: 72
---



## Project Introduction

Inspired by my son's love for everything solar system and my wanting to play with [Shiny Assistant.](https://gallery.shinyapps.io/assistant/) I've built an app that takes your date of birth and displays your age for each of the major planets in our solar system. 

## How to Use

Upon launching the app, you'll see all the controls on the left hand side. Add your date of birth to automatically display your solar ages. 

There's also a toggle to switch to days calculation mode which requires a little explanation.

### Calculation Mode

Year: Gives your Earth age based on a planets orbit.

Day: Gives your Earth age based on a planets rotation.

Examples of how the calculation modes work using someone born 27th May 2005.


```r
# Use age is converted to Earth days
age_difference_in_earth_days <- Sys.Date() -  ymd("2005-05-27")

# The Year calculation divides and floors by the number of Earth days of 
# a planet.
# Let's use Jupiter which orbits around the Sun every ~4331 Earth days.

jupiter_age_year_calc <- floor(age_difference_in_earth_days / 4331.984) |>  
 as.numeric()

# Jupiter age of 20 year old by years calculation
jupiter_age_year_calc
```

```
## [1] 1
```

```r
# The Days calculation converts your days Earth age to a planets equivalent. 
# Then divides and floors by an Earth year.
# Let's use Jupiter which rotates around it's axis every ~9.8 Earth hours

age_difference_in_jupiter_days <- age_difference_in_earth_days / (9.8/24)

jupiter_age_days_calc <- floor(age_difference_in_jupiter_days / 365.26) |>  
 as.numeric()

# Jupiter age of 20 year old by days calculation
jupiter_age_days_calc
```

```
## [1] 48
```

Venus inspired the days-based calculation with it's day longer than it's year. Still wrapping my head around that. 

## Dashboard

Well here it is, the **[Solar System Age Converter](https://kurtisasha.shinyapps.io/solar-system-ages/).** 

The app was the result of a 2 hour Shiny Assistant assisted play around, a feat I would have spent quadruple the time on. I am a fan of Shiny Assistant but I have notes:

- Functions were sometimes implemented in code without calling the library.
- It got stuck on the trickier css elements, in my case shadow-root constructed stylesheets.
- I wish a button was introduced to take me to the editor rather than typing "Open the editor".
- I could not clear the console.
- I could use control + f but couldn't close.

It is probable I missed a bunch of functionality in my speed run and I'll aim to read around, see how others have fared.  

## Acknowledgements

[Winston Chang](https://github.com/wch) for Shiny Assistant.

[Royal Museums Greenwich](https://www.rmg.co.uk/stories/space-astronomy/solar-system-data) for solar system information.

[r-bloggers.com](r-bloggers.com) for the reach, platform, and content

Packages and package maintainer(s):

-   lubridate \| Vitalie Spinu
