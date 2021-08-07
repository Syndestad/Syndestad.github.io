---
title: Chi-square in R
author: Synnøve Yndestad
date: '2021-08-07'
slug: []
categories: ["R", "Statistics", "Clinical science"]
tags: ["Chi-square","Categoricals","Crosstab", "Non-parametric", "assocplot()", "chisq.test()"]
---

<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>

The Chi-square test is used to compare differences between two or more categorical variables. All variables must be ordinal or nominal and summarized as a frequency table.
It is a non-parametric test, meaning that it is suitable also for data that is not normally distributed.

Some of the assumptions for performing a Chi-square test are:
- Each observation is independent of all the others (one observation per subject), and the categories must be mutually exclusive so that a subject fits into only one of the categories. I.e Small/Medium/Large, or Mutated/Wild-type.
- The value of the **expected** count should be &gt;=5 in minimum 80% of the cells, and no cells should have 0 or negative counts.

For more details, see PMID:[23894860](https://pubmed.ncbi.nlm.nih.gov/23894860/)

To run a Chi-square test i R, we first need some data.
The “esoph” dataset in R´s datasets-package contains data from a case control study of oesophaeal cancer, where records for 88 age-group/alcohol-consumption/tobacco-consumption combinations are listed with the frequency of number of cases (ncases) and number of controls (ncontrols).

Fetch the esoph-dataset, take a look of the data-structure and data.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.2     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   1.4.0     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
data(esoph)
head(esoph)
```

    ##   agegp     alcgp    tobgp ncases ncontrols
    ## 1 25-34 0-39g/day 0-9g/day      0        40
    ## 2 25-34 0-39g/day    10-19      0        10
    ## 3 25-34 0-39g/day    20-29      0         6
    ## 4 25-34 0-39g/day      30+      0         5
    ## 5 25-34     40-79 0-9g/day      0        27
    ## 6 25-34     40-79    10-19      0         7

``` r
str(esoph)
```

    ## 'data.frame':    88 obs. of  5 variables:
    ##  $ agegp    : Ord.factor w/ 6 levels "25-34"<"35-44"<..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ alcgp    : Ord.factor w/ 4 levels "0-39g/day"<"40-79"<..: 1 1 1 1 2 2 2 2 3 3 ...
    ##  $ tobgp    : Ord.factor w/ 4 levels "0-9g/day"<"10-19"<..: 1 2 3 4 1 2 3 4 1 2 ...
    ##  $ ncases   : num  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ ncontrols: num  40 10 6 5 27 7 4 7 2 1 ...

The data structure tells us that the age-group/alcohol-consumption/tobacco-consumption columns are ordered factors with 6, 4 and 4 levels respectively, while the cases and control columns are numerical.

``` r
DT::datatable(esoph, options = list(searching = FALSE))
```

<div id="htmlwidget-1" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57","58","59","60","61","62","63","64","65","66","67","68","69","70","71","72","73","74","75","76","77","78","79","80","81","82","83","84","85","86","87","88"],["25-34","25-34","25-34","25-34","25-34","25-34","25-34","25-34","25-34","25-34","25-34","25-34","25-34","25-34","25-34","35-44","35-44","35-44","35-44","35-44","35-44","35-44","35-44","35-44","35-44","35-44","35-44","35-44","35-44","35-44","45-54","45-54","45-54","45-54","45-54","45-54","45-54","45-54","45-54","45-54","45-54","45-54","45-54","45-54","45-54","45-54","55-64","55-64","55-64","55-64","55-64","55-64","55-64","55-64","55-64","55-64","55-64","55-64","55-64","55-64","55-64","55-64","65-74","65-74","65-74","65-74","65-74","65-74","65-74","65-74","65-74","65-74","65-74","65-74","65-74","65-74","65-74","75+","75+","75+","75+","75+","75+","75+","75+","75+","75+","75+"],["0-39g/day","0-39g/day","0-39g/day","0-39g/day","40-79","40-79","40-79","40-79","80-119","80-119","80-119","120+","120+","120+","120+","0-39g/day","0-39g/day","0-39g/day","0-39g/day","40-79","40-79","40-79","40-79","80-119","80-119","80-119","80-119","120+","120+","120+","0-39g/day","0-39g/day","0-39g/day","0-39g/day","40-79","40-79","40-79","40-79","80-119","80-119","80-119","80-119","120+","120+","120+","120+","0-39g/day","0-39g/day","0-39g/day","0-39g/day","40-79","40-79","40-79","40-79","80-119","80-119","80-119","80-119","120+","120+","120+","120+","0-39g/day","0-39g/day","0-39g/day","0-39g/day","40-79","40-79","40-79","80-119","80-119","80-119","80-119","120+","120+","120+","120+","0-39g/day","0-39g/day","0-39g/day","40-79","40-79","40-79","40-79","80-119","80-119","120+","120+"],["0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","30+","0-9g/day","10-19","20-29","30+","0-9g/day","10-19","0-9g/day","10-19"],[0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,1,0,0,0,3,1,0,0,0,0,0,2,0,2,1,0,0,0,6,4,5,5,3,6,1,2,4,3,2,4,2,3,3,4,9,6,4,3,9,8,3,4,5,6,2,5,5,4,2,0,17,3,5,6,4,2,1,3,1,1,1,1,2,1,2,1,0,1,1,1,2,1],[40,10,6,5,27,7,4,7,2,1,2,1,0,1,2,60,13,7,8,35,20,13,8,11,6,2,1,1,3,2,45,18,10,4,32,17,10,2,13,8,4,2,0,1,1,0,47,19,9,2,31,15,13,3,9,7,3,0,5,1,1,1,43,10,5,2,17,7,4,7,8,1,0,1,1,0,0,17,4,2,3,2,3,0,0,0,0,0]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>agegp<\/th>\n      <th>alcgp<\/th>\n      <th>tobgp<\/th>\n      <th>ncases<\/th>\n      <th>ncontrols<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"searching":false,"columnDefs":[{"className":"dt-right","targets":[4,5]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

Some data transformation is necessary. Lets make a frequency table for alcohol consumption. Then we need to group by alcohol consumption level, and summarize cases and controls within each group.

``` r
alcohol <- esoph %>% select(c(-"tobgp", -"agegp")) %>%        # Remove unnecessary columns
                     group_by(alcgp) %>%                      # Group by alcohol consumption levels
                     mutate(ncases = sum(ncases)) %>%         # Summarize cases of cancer within alcohol consumption level-groups
                     mutate(ncontrols = sum(ncontrols)) %>%   # Summarize cases of controls within alcohol consumption level-groups
                     distinct()                               # Remove duplicate values
alcohol <- as.data.frame(alcohol)
```

Turn the alcgp-column into column names:

``` r
alcgp <- alcohol$alcgp
row.names(alcohol) <- alcgp
alcohol <- alcohol[,-1]
alcohol
```

    ##           ncases ncontrols
    ## 0-39g/day     29       386
    ## 40-79         75       280
    ## 80-119        51        87
    ## 120+          45        22

``` r
DT::datatable(alcohol, options = list(searching = FALSE))
```

<div id="htmlwidget-2" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-2">{"x":{"filter":"none","data":[["0-39g/day","40-79","80-119","120+"],[29,75,51,45],[386,280,87,22]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>ncases<\/th>\n      <th>ncontrols<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"searching":false,"columnDefs":[{"className":"dt-right","targets":[1,2]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

Now, perform the Chi-square test on the frequency table and save test results as “alcoholChi”:

``` r
alcoholChi <- chisq.test(alcohol)
alcoholChi
```

    ## 
    ##  Pearson's Chi-squared test
    ## 
    ## data:  alcohol
    ## X-squared = 158.95, df = 3, p-value < 2.2e-16

The p-value is 2.2e-16, and considered significant. We can therefore assume that there is an association between alcohol consumption and oesophaeal cancer.

If the chi squared test is significant we need to take a look at the residuals to figure out the nature of the association.

Print the observed and expected counts from the test, and the residuals:

``` r
# observed counts 
alcoholChi$observed  
```

    ##           ncases ncontrols
    ## 0-39g/day     29       386
    ## 40-79         75       280
    ## 80-119        51        87
    ## 120+          45        22

``` r
# expected counts under the null hypothesis
alcoholChi$expected  
```

    ##             ncases ncontrols
    ## 0-39g/day 85.12821 329.87179
    ## 40-79     72.82051 282.17949
    ## 80-119    28.30769 109.69231
    ## 120+      13.74359  53.25641

``` r
# Pearson residuals  (observed - expected) / sqrt(expected).
alcoholChi$residuals  
```

    ##               ncases  ncontrols
    ## 0-39g/day -6.0833726  3.0903564
    ## 40-79      0.2554039 -0.1297453
    ## 80-119     4.2650726 -2.1666591
    ## 120+       8.4311925 -4.2830501

When the Pearson residuals is above 2, the cells observed frequency is more than the expected frequency. When the Pearson residuals is less than -2, the cells observed frequency is less than the expected frequency.
This enables us to see which cells are contributing for the association, and in which direction (negative or positive).

To visualize associations, we can use the nifty `assocplot()` function. It plots a "Cohen–Friendly association plot and it takes a contingency table as input, and displays the deviations from independence as a bar chart.

See `??assocplot()` for details.

assocplot read data as matrix. Therefore, transform the data.frame into matrix inside the call:

``` r
assocplot(as.matrix(alcohol), xlab = "Alcohol consuption", main="Association between alcohol consumption and oesophaeal cancer")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-1.png" width="672" />

The height of the box represents the contribution of each cell into the total Chi-squared.  
The area of the box is proportional to the difference in observed and expected frequencies.
Thus, a wide and tall box indicates a stronger association.

According to these results, there is a clear association between high alcohol consumption and oesophaeal cancer.
Does this mean that we should stop drinking wine completely?

According to [Av og til](https://avogtil.no/fakta/en-alkoholenhet/), 1 unit of alcohol is 12g. That is in more commonly known units;  
- One bottle of beer (33 cl) 4.5 vol %  
- A small glass of wine (12.5 cl) 12 vol %  
- A very small glass of liquor (4 cl) 40 vol %

The association was significant for cases with an alcohol-consumption of more than 80g/day. This equals more than 6 units a day. 6 units is approximately 2.2 L beer, or 83.25 cl wine (i.e more than a bottle (75cl)), or 26.64 cl liquor every day. That amount would be unhealthy also beyond increased cancer risk. So if you enjoy your Friday evening glass, have no worries, as long it is in moderation.

## When not to use Chi-square

In some cases it is inappropriate to use the Chi-square test.

Using data listed in [table two](https://doi.org/10.1016/j.annonc.2020.11.009) we could try to test if patients that have tumors with HR mutations responds better to treatment than wild-type tumors that is able to repair DNA by HR.

First, create a 2x2 table from vectors.

``` r
HR_mutation <- c(10, 1, 0)  
Wt <- c(8, 10, 3)
df = rbind(HR_mutation, Wt)             # Bind vector by rows to a data frame
colnames(df) <- c("CR+PR", "SD", "PD")  # Add response values as column names
df                                      # print data frame
```

    ##             CR+PR SD PD
    ## HR_mutation    10  1  0
    ## Wt              8 10  3

``` r
chisq.test(df)
```

    ## Warning in chisq.test(df): Chi-squared approximation may be incorrect

    ## 
    ##  Pearson's Chi-squared test
    ## 
    ## data:  df
    ## X-squared = 8.2683, df = 2, p-value = 0.01602

As we see from the output warning, the Chi-squared approximation may be incorrect. This is because this test is sensitive to sample size. A common rule of thumb is to have no cells with an expected count of zero, 5 or more in all cells in 2x2 tables or that 80% of cells have more than 5 in larger tables.
In this case a Fisher test (after transforming the data to a 2x2 table) is more appropriate due to small sample size, or a test for trends in proportions, as the values are ordinal.

When the number of categories for each variable becomes very large (more than 20) it becomes difficult to interpret the results.
Also, when the sample size is very large (&gt;500) any small difference can produce significant results causing [Type 1 errors](https://en.wikipedia.org/wiki/Type_I_and_type_II_errors) or false positives. Just because a difference is significant, the difference may be so small that it is biologically irrelevant. So interpret your results with care.
