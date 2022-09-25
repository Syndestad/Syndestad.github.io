---
title: For loop for Multiple Trend in Proportions
author: R package build
date: '2022-09-25'
slug: []
categories:
  - R
  - Statistics
  - Clinical science
tags:
  - prop.trend.test
  - tidyverse
  - Chi-square
  - Categoricals
  - for-loop
  - write.xlsx()
subtitle: ''
excerpt: 'When you have only one parameter to test, following my previous tutorial for test for trends in proportions will be sufficient. However, if you have many independent variables to be tested across several dependent variables, it may become quite tedious to do them all one by one. Therefore, I wrote a for-loop that will create all the summary-tables, perform the test for trends in proportions for each table, add the test result to the count matrix and save the output neatly in a csv/excel format. Here I explain each step in the process.'
draft: no
series: ~
layout: single
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

When you have only one parameter to test, following the [tutorial](https://syndestad.netlify.app/blog/2021-08-09-test-for-trend-in-proportions/) for test for trends in proportions or using the online calculator [here](https://epitools.ausvet.com.au/trend) will be sufficient.
However, if you have many independent variables to be tested across several dependent variables, it may become quite tedious to do them all one by one.
Therefore, I wrote a for-loop that will create all the summary-tables, perform the test for trends in proportions for each table, add the test result to the count matrix and save the output neatly in a csv/excel format. Here I explain each step in the process.

## Import your data.

Load the necessary packages.

``` r
library(tidyverse)
library(clinfun)
```

``` r
# Generate random input data:
df = tibble(Subject = LETTERS[1:20],
            Response = sample(x = c("CR", "PR", "SD", "PD"), size  = 20, replace = TRUE),
            Biomarker1 =  sample(x = c("High", "Low"), size  = 20, replace = TRUE),
            Biomarker2 =  sample(x = c("High", "Low"), size  = 20, replace = TRUE),
            Biomarker3 =  sample(x = c("Wt", "Mutated"), size  = 20, replace = TRUE))

# Set factor levels
df$Response = factor(df$Response,levels = c("CR", "PR", "SD", "PD"))
df$Biomarker1 = factor(df$Biomarker1, levels = c("High", "Low")) 
df$Biomarker2 = factor(df$Biomarker2, levels = c("High", "Low")) 
df$Biomarker3 = factor(df$Biomarker3, levels = c("Wt", "Mutated")) 

head(df)
```

    ## # A tibble: 6 × 5
    ##   Subject Response Biomarker1 Biomarker2 Biomarker3
    ##   <chr>   <fct>    <fct>      <fct>      <fct>     
    ## 1 A       SD       Low        Low        Mutated   
    ## 2 B       SD       Low        Low        Mutated   
    ## 3 C       PR       Low        Low        Wt        
    ## 4 D       CR       Low        Low        Wt        
    ## 5 E       CR       Low        Low        Wt        
    ## 6 F       PD       Low        Low        Mutated

Your dependent variable, in this case “Response”, need to be in correct order for the prop.trend.test(). We want CR=1, PR=2, SD=3 and PD=4.
Verify that the levels are in correct order with `levels()`, and adjust with `factor()` if it needs to be corrected.

``` r
levels(df$Response)
```

    ## [1] "CR" "PR" "SD" "PD"

## Create an output directory and **name your output-file**:

First, name your `OutputFileName` appropriately.
An appropriate name may include **which data** is used, the **method** used to generate it and the **date** it was created. Avoid white space and special characters in names.

The next line will create an output folder in your working directory `here::here()`. If the folder already exist, it will print that the folder already exist.  
Then it will set the path relative to `here::here()` with the full name of the final output file with a csv-extension using the OutputFileName variable.

``` r
# Specify name of output file
OutputFileName = "MyResults_TestForTrendInProportions"

# Create output folder if it does not already exist.
if (!dir.exists("output")){dir.create("output")} else {print(paste0("output", " already exists!"))}
```

    ## [1] "output already exists!"

``` r
# Set output filepath relative to here::here()
outputFile = here::here(paste0("output/",OutputFileName, ".csv"))
```

Choose the variables from your data.frame to include in the `prop.trend.test()`.  
The function will loop over all independent variables and fetch the names from the **IndependentVariableName** vector for the final output. Therefore, it is important to add the names in **the same order** as the variables are named from.
Adding a separate vector for the names allows us to specify the names to be different from how they appear in the column names.

``` r
IndependentVariableName =  c("Biomarker Nr1", "Biomarker Nr2", "Biomarker Nr3")  # Cause, variable name to be put in the table
DependentVariableName =    c("Response")                                         # Effect, variable name to be put in the table

# Create dataframe with selected variables
IndependentVariable = df %>% select("Biomarker1", "Biomarker2", "Biomarker3") # Cause variable(s) 
DependentVariable = df$Response                                               # Effect variable

# Have a look at the variables to be tested:
head(IndependentVariable)
```

    ## # A tibble: 6 × 3
    ##   Biomarker1 Biomarker2 Biomarker3
    ##   <fct>      <fct>      <fct>     
    ## 1 Low        Low        Mutated   
    ## 2 Low        Low        Mutated   
    ## 3 Low        Low        Wt        
    ## 4 Low        Low        Wt        
    ## 5 Low        Low        Wt        
    ## 6 Low        Low        Mutated

## The for-loop

Now, run the for-loop that calculates test for trends in proportion for each variable iteratively, and saves the output including all summary variables.

``` r
# For every IndependentVariable, do:
for (i in seq_along(IndependentVariable)) {
# Print the name for visual control
print(IndependentVariableName[[i]])
# Create a table of count data of the independent and dependent variable:  
tbl = table(IndependentVariable[[i]], DependentVariable)
 
print(tbl)

# Get the number of counts in the table
n = colSums(tbl)

# Do the Proportional Trend Test for the first row in the table
ptt1 = prop.trend.test(tbl[1,], n, score = seq_along(tbl[1,])) 
# The result for row 1 and 2 should be equal
#ptt2 = prop.trend.test(tbl[2,], n, score = seq_along(tbl[2,])) 
print(ptt1)

# Use broom to save a tidy output of the test:
t.tt = broom::tidy(ptt1)
# Edit name of statistic output
colnames(t.tt)[colnames(t.tt) == "statistic"] = "X-squared"
# Add the name of the variables used for the test result
t.tt$DependentVariable = DependentVariableName
t.tt$IndependentVariable = IndependentVariableName[[i]]

# Reorder columns and remove parameter column
t.tt = t.tt %>% select(DependentVariable, IndependentVariable, everything(), -parameter)
# Add empty row since the count data has two rows and the test has one.
t.tt[nrow(t.tt)+1,] <- NA
print(t.tt)

# Add the Summary Table to test result
## Turn the table into a data-frame
tbl.m = tbl %>% as.data.frame.matrix() 
## Add the rownames
tbl.m$Condition = rownames(tbl)
## Add the table to the test-results by cbind
output = tbl.m %>% cbind(t.tt)
# Reorder output for estetics
output = output %>% select(DependentVariable,   IndependentVariable, Condition, everything())
print(output)

# Write results to outputFile. Append results to outputFile one sample pr loop
  write.table(output, file = outputFile, sep = ",", 
              row.names = FALSE, col.names=!file.exists(outputFile),
              quote = FALSE, append = TRUE)

}
```

    ## [1] "Biomarker Nr1"
    ##       DependentVariable
    ##        CR PR SD PD
    ##   High  1  1  3  3
    ##   Low   4  1  3  4
    ## 
    ##  Chi-squared Test for Trend in Proportions
    ## 
    ## data:  tbl[1, ] out of n ,
    ##  using scores: 1 2 3 4
    ## X-squared = 0.6006, df = 1, p-value = 0.4383
    ## 
    ## # A tibble: 2 × 5
    ##   DependentVariable IndependentVariable `X-squared` p.value method              
    ##   <chr>             <chr>                     <dbl>   <dbl> <chr>               
    ## 1 Response          Biomarker Nr1             0.601   0.438 Chi-squared Test fo…
    ## 2 <NA>              <NA>                     NA      NA     <NA>                
    ##      DependentVariable IndependentVariable Condition CR PR SD PD X-squared
    ## High          Response       Biomarker Nr1      High  1  1  3  3 0.6006006
    ## Low               <NA>                <NA>       Low  4  1  3  4        NA
    ##       p.value                                    method
    ## High 0.438349 Chi-squared Test for Trend in Proportions
    ## Low        NA                                      <NA>

    ## Warning in write.table(output, file = outputFile, sep = ",", row.names =
    ## FALSE, : appending column names to file

    ## [1] "Biomarker Nr2"
    ##       DependentVariable
    ##        CR PR SD PD
    ##   High  2  0  0  3
    ##   Low   3  2  6  4
    ## 
    ##  Chi-squared Test for Trend in Proportions
    ## 
    ## data:  tbl[1, ] out of n ,
    ##  using scores: 1 2 3 4
    ## X-squared = 0.012012, df = 1, p-value = 0.9127
    ## 
    ## # A tibble: 2 × 5
    ##   DependentVariable IndependentVariable `X-squared` p.value method              
    ##   <chr>             <chr>                     <dbl>   <dbl> <chr>               
    ## 1 Response          Biomarker Nr2            0.0120   0.913 Chi-squared Test fo…
    ## 2 <NA>              <NA>                    NA       NA     <NA>                
    ##      DependentVariable IndependentVariable Condition CR PR SD PD  X-squared
    ## High          Response       Biomarker Nr2      High  2  0  0  3 0.01201201
    ## Low               <NA>                <NA>       Low  3  2  6  4         NA
    ##        p.value                                    method
    ## High 0.9127271 Chi-squared Test for Trend in Proportions
    ## Low         NA                                      <NA>
    ## [1] "Biomarker Nr3"
    ##          DependentVariable
    ##           CR PR SD PD
    ##   Wt       4  1  1  0
    ##   Mutated  1  1  5  7
    ## 
    ##  Chi-squared Test for Trend in Proportions
    ## 
    ## data:  tbl[1, ] out of n ,
    ##  using scores: 1 2 3 4
    ## X-squared = 9.6525, df = 1, p-value = 0.001891
    ## 
    ## # A tibble: 2 × 5
    ##   DependentVariable IndependentVariable `X-squared`  p.value method             
    ##   <chr>             <chr>                     <dbl>    <dbl> <chr>              
    ## 1 Response          Biomarker Nr3              9.65  0.00189 Chi-squared Test f…
    ## 2 <NA>              <NA>                      NA    NA       <NA>               
    ##         DependentVariable IndependentVariable Condition CR PR SD PD X-squared
    ## Wt               Response       Biomarker Nr3        Wt  4  1  1  0   9.65251
    ## Mutated              <NA>                <NA>   Mutated  1  1  5  7        NA
    ##             p.value                                    method
    ## Wt      0.001890931 Chi-squared Test for Trend in Proportions
    ## Mutated          NA                                      <NA>

Now we can look at the result by importing the outputFile with `read_csv()`

``` r
TheOutput = read_csv(outputFile)
```

``` r
library(DT)
datatable(TheOutput, 
              rownames = FALSE, 
              colnames = colnames(TheOutput),
              options = list(searching=FALSE)) %>%
              formatRound(columns=c('X-squared', 'p.value'), digits=3)
```

<div id="htmlwidget-1" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"filter":"none","vertical":false,"data":[["Response",null,"Response",null,"Response",null],["Biomarker Nr1",null,"Biomarker Nr2",null,"Biomarker Nr3",null],["High","Low","High","Low","Wt","Mutated"],[1,4,2,3,4,1],[1,1,0,2,1,1],[3,3,0,6,1,5],[3,4,3,4,0,7],[0.600600600600602,null,0.0120120120120121,null,9.65250965250966,null],[0.438348961461724,null,0.912727146108879,null,0.00189093067828876,null],["Chi-squared Test for Trend in Proportions",null,"Chi-squared Test for Trend in Proportions",null,"Chi-squared Test for Trend in Proportions",null]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>DependentVariable<\/th>\n      <th>IndependentVariable<\/th>\n      <th>Condition<\/th>\n      <th>CR<\/th>\n      <th>PR<\/th>\n      <th>SD<\/th>\n      <th>PD<\/th>\n      <th>X-squared<\/th>\n      <th>p.value<\/th>\n      <th>method<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"searching":false,"columnDefs":[{"targets":7,"render":"function(data, type, row, meta) {\n    return type !== 'display' ? data : DTWidget.formatRound(data, 3, 3, \",\", \".\", null);\n  }"},{"targets":8,"render":"function(data, type, row, meta) {\n    return type !== 'display' ? data : DTWidget.formatRound(data, 3, 3, \",\", \".\", null);\n  }"},{"className":"dt-right","targets":[3,4,5,6,7,8]}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":["options.columnDefs.0.render","options.columnDefs.1.render"],"jsHooks":[]}</script>

Optionally, save the final results as an excel file.

``` r
openxlsx::write.xlsx(TheOutput, paste0("output/", OutputFileName, ".xlsx"))
```

Boom! You have completed your task in no time! Now go find out what the results mean.

``` r
sessionInfo()
```

    ## R version 4.2.1 (2022-06-23)
    ## Platform: x86_64-apple-darwin17.0 (64-bit)
    ## Running under: macOS Big Sur ... 10.16
    ## 
    ## Matrix products: default
    ## BLAS:   /Library/Frameworks/R.framework/Versions/4.2/Resources/lib/libRblas.0.dylib
    ## LAPACK: /Library/Frameworks/R.framework/Versions/4.2/Resources/lib/libRlapack.dylib
    ## 
    ## locale:
    ## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] DT_0.23         clinfun_1.1.0   forcats_0.5.1   stringr_1.4.0  
    ##  [5] dplyr_1.0.9     purrr_0.3.4     readr_2.1.2     tidyr_1.2.0    
    ##  [9] tibble_3.1.7    ggplot2_3.3.6   tidyverse_1.3.1
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] Rcpp_1.0.8.3      lubridate_1.8.0   here_1.0.1        mvtnorm_1.1-3    
    ##  [5] assertthat_0.2.1  rprojroot_2.0.3   digest_0.6.29     utf8_1.2.2       
    ##  [9] R6_2.5.1          cellranger_1.1.0  backports_1.4.1   reprex_2.0.1     
    ## [13] evaluate_0.15     httr_1.4.3        blogdown_1.10     pillar_1.7.0     
    ## [17] rlang_1.0.5       readxl_1.4.0      rstudioapi_0.13   jquerylib_0.1.4  
    ## [21] rmarkdown_2.14    htmlwidgets_1.5.4 bit_4.0.4         munsell_0.5.0    
    ## [25] broom_1.0.0       compiler_4.2.1    modelr_0.1.8      xfun_0.31        
    ## [29] pkgconfig_2.0.3   htmltools_0.5.3   tidyselect_1.1.2  bookdown_0.27    
    ## [33] fansi_1.0.3       crayon_1.5.1      tzdb_0.3.0        dbplyr_2.2.1     
    ## [37] withr_2.5.0       grid_4.2.1        jsonlite_1.8.0    gtable_0.3.0     
    ## [41] lifecycle_1.0.1   DBI_1.1.3         magrittr_2.0.3    scales_1.2.0     
    ## [45] zip_2.2.0         cli_3.3.0         stringi_1.7.6     vroom_1.5.7      
    ## [49] fs_1.5.2          xml2_1.3.3        bslib_0.3.1       ellipsis_0.3.2   
    ## [53] generics_0.1.3    vctrs_0.4.1       openxlsx_4.2.5    tools_4.2.1      
    ## [57] bit64_4.0.5       glue_1.6.2        crosstalk_1.2.0   hms_1.1.1        
    ## [61] parallel_4.2.1    fastmap_1.1.0     yaml_2.3.5        colorspace_2.0-3 
    ## [65] rvest_1.0.2       knitr_1.39        haven_2.5.0       sass_0.4.1
