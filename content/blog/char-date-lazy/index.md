---
title: "Character to Date conversion using `lazy` queries"
author: "Author: Kurtis Smith"
date: "2025-04-25"
slug: char-date-lazy
output: 
 html_document:
  fig_width: 10
  fig_height: 6
categories:
  - self-tech-support
tags:
  - dbplyr
  - rstats
summary: "How to convert a character string to date format through a lazy query without saving data to local memory"
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
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>

## Problem

How to convert a string with format DDMMYYYY, to a date type using **lazy queries**. The problem is that base r function `as.Date()` does not have a translatable SQL equivalent. More information on this is detailed in this [sql translation article](https://dbplyr.tidyverse.org/articles/sql-translation.html#known-functions).

This problem is largely addressed in the [special forms article](https://dbplyr.tidyverse.org/articles/translation-function.html#special-forms). However, no example is given for converting a character to a date data type. This post will provide this example.

Like most of my self described “self-tech-support” post categories, it was introduced through work. Took me a while to come to the answer so here is to saving someone sometime somewhere.

Let’s begin with simulating a lazy connection.

``` r
# Load packages
library(dplyr)
library(dbplyr)
library(DT)

# Create a local lazy tibble with Postgres SQL connection
test_tbl <- lazy_frame(char_date = c("01012020", "01022020", "01032020"),
                       con = simulate_postgres())

test_tbl
```

    ## <SQL>
    ## SELECT *
    ## FROM `df`

# Solution

The below provides two workable examples or at least the translations. Whilst you can denote a SQL function by using uppercase as shown in example 1, the second example does provide an explicit marker for what is to be used as-is in SQL with function `sql()`.

``` r
# example 1 
mutate(test_tbl, date = TO_DATE(char_date, 'DDMMYYYY'))
```

    ## <SQL>
    ## SELECT `df`.*, TO_DATE(`char_date`, 'DDMMYYYY') AS `date`
    ## FROM `df`

``` r
# example 2
mutate(test_tbl, date = sql("TO_DATE(char_date, 'DDMMYYYY')"))
```

    ## <SQL>
    ## SELECT `df`.*, TO_DATE(char_date, 'DDMMYYYY') AS `date`
    ## FROM `df`

# Example (kind of)

It’s “kind of” because it’s not a direct translation applied to SQL but creating an example without having an instance of SQL is difficult. At least for free. I’ll provide instruction below if you wish to follow along:

1.  Head to [DB Fiddle](https://www.db-fiddle.com/)
2.  Change SQL in the top left to Postgres SQL version 17
3.  Copy & paste below in **Schema SQL** box

<!-- -->

    CREATE TABLE test_tbl (
      char_date varchar(8) NOT NULL
    );
    INSERT INTO test_tbl (char_date) VALUES ('01012020');
    INSERT INTO test_tbl (char_date) VALUES ('01022020');
    INSERT INTO test_tbl (char_date) VALUES ('01032020');

4.  Copy & paste below in **Query SQL** box

<!-- -->

    SELECT char_date, pg_typeof(char_date) AS type
    FROM test_tbl;

    SELECT TO_DATE(char_date, 'DDMMYYYY') AS char_date, 
           pg_typeof(TO_DATE(char_date, 'DDMMYYYY')) AS type 
    FROM test_tbl;

**Query \#1**

<div class="datatables html-widget html-fill-item" id="htmlwidget-1" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"filter":"none","vertical":false,"data":[["01012020","01022020","01032020"],["character varying","character varying","character varying"]],"container":"<table class=\"compact hover row-border\">\n  <thead>\n    <tr>\n      <th>char_date<\/th>\n      <th>type<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"dom":"t","columnDefs":[{"width":"200px","targets":"_all"},{"name":"char_date","targets":0},{"name":"type","targets":1}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

**Query \#2**

<div class="datatables html-widget html-fill-item" id="htmlwidget-2" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-2">{"x":{"filter":"none","vertical":false,"data":[["2020-01-01","2020-02-01","2020-03-01"],["date","date","date"]],"container":"<table class=\"compact hover row-border\">\n  <thead>\n    <tr>\n      <th>char_date<\/th>\n      <th>type<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"dom":"t","columnDefs":[{"width":"200px","targets":"_all"},{"name":"char_date","targets":0},{"name":"type","targets":1}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

Query 2 shows the output of the translated mutate from **Solution** albeit manually added into SQL.

# Consider

The flavour of SQL for this example was be PostgreSQL version 17. Not all SQL flavours support the `TO_DATE` function, below is a short and not exhaustive list.

<div class="datatables html-widget html-fill-item" id="htmlwidget-3" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-3">{"x":{"filter":"none","vertical":false,"data":[["Oracle","PostgreSQL","IBM Informix","Snowflake","Amazon Redshift","Teradata","InterSystems IRIS","MySQL","SQL Server","SQLite"],["TO_DATE(char, format)","TO_DATE(text, format)","TO_DATE(char_expression, format_string)","TO_DATE(expression[, format])","TO_DATE(string, format[, is_strict])","TO_DATE(string_expr, format_arg)","TO_DATE(date_string, format)","STR_TO_DATE(string, format)","CONVERT(DATE, string, style) / CAST(string AS DATE)","DATE(time_value[, modifier,…])"],["Yes","Yes","Yes","Yes","Yes","Yes","Yes","No","No","No"]],"container":"<table class=\"compact hover row-border\">\n  <thead>\n    <tr>\n      <th>dialect<\/th>\n      <th>function_syntax<\/th>\n      <th>to_date_supported<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"dom":"t","columnDefs":[{"name":"dialect","targets":0},{"name":"function_syntax","targets":1},{"name":"to_date_supported","targets":2}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

# Conclusion

Lazy all the way! My blocker, this date field as a character, was keeping my lazy queries from continued laziness. Thankfully there was a workaround. The blog does miss the step of directly showing the translated mutate query a SQL database but I hope this example from two sides helps.

## Acknowledgements

[r-bloggers.com](r-bloggers.com) for the reach, platform, and content

[DB Fiddle](https://www.db-fiddle.com/) for SQL sandpit

Packages and package maintainer(s):

- dplyr \| Hadley Wickham
- dbplyr \| Hadley Wickham
- DT \| Joe Cheng
