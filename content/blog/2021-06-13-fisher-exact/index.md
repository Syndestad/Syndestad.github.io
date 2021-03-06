---
title: Fisher-Exact in R
subtitle: "A short guide on performing the Fisher-Exact test and Summarizing a 2x2 table of categorical values."
author: Synnøve Yndestad
date: '2021-07-10'
#slug: []
categories: ["R", "Statistics", "Clinical science"]
tags: ["Categoricals", "Non-parametric", "fisher.test()", "Crosstab", "2x2-table"]
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

Disregarding the [problematic](https://www.ucl.ac.uk/biosciences/gee/ucl-centre-computational-biology/ronald-aylmer-fisher-1890-1962) side of Fisher, the statistical methods he developed are still very useful. Read any clinical paper, and I guarantee you that a Fisher exact test has been performed.

<!-- this is a subheadline -->

Fisher-Exact is a statistical test used for 2x2 contingency tables of categorical data. It is particularity useful for small sample sizes where other tests, like the Chi square test would be unsuitable.

## Fisher-Exact from a 2x2 table:

First you need to enter your data, and I will use some real life examples.

The research group that I am a part of, have performed a clinical study where patients with triple negative breast cancer have received olaparib mono therapy.
Olaparib is a PARP inhibitor, and by inhibiting PARP, cells become dependent on repairing DNA
through homologous recombination (HR). If they lack the ability to repair DNA, they will die.
Using Fisher exact, we can test if patients that have tumors with HR deficiency responds better to treatment than tumors that can repair DNA by HR. HR deficiency in this context may be caused either by having an inheritable germline HR mutation, by having a mutation in HR
genes in the tumor tissue, or epigenetically silenced by methylation of *BRCA1* in the tumor tissue.

Using data listed in [table two](https://doi.org/10.1016/j.annonc.2020.11.009) (Subgroup analysis nr 4) we can create a 2x2 table from vectors.

Make two vectors of count data in the desired order like this; c(“Responder,” “Non-Responder”)

``` r
HR_deficient = c(16, 4)
HR_proficient = c(2, 10)
```

Turn the two vectors into a Data Frame, nested inside a transpose t().

``` r
aTable = t(data.frame(HR_deficient,HR_proficient))
```

Have a look at your table

``` r
aTable                                     
```

    ##               [,1] [,2]
    ## HR_deficient    16    4
    ## HR_proficient    2   10

You can change column or row-names on your table using dimnames(). Use \[\[1\]\] to specify row-names and \[\[2\]\] for column names.

**Add/change column-names:**  
dimnames(aTable)\[\[2\]\] = c(“New Column-name A” , “New Column-name B”)  
**Add/change row-names:**  
dimnames(aTable)\[\[1\]\] = c(“New Row-name A” , “New Row-name B”)

``` r
dimnames(aTable)[[2]] = c("Responder", "Non-responder")  #Add/change column-names
```

Have a look

``` r
aTable
```

    ##               Responder Non-responder
    ## HR_deficient         16             4
    ## HR_proficient         2            10

Run the Fisher exact test by **fisher.test()** and inspect your result.

``` r
fisher.test(aTable)
```

    ## 
    ##  Fisher's Exact Test for Count Data
    ## 
    ## data:  aTable
    ## p-value = 0.0007899
    ## alternative hypothesis: true odds ratio is not equal to 1
    ## 95 percent confidence interval:
    ##    2.459807 228.297235
    ## sample estimates:
    ## odds ratio 
    ##   17.57049

This test is significant with a p value = **0.0008!**  
This tells us that the null hypothesis is false, and that there there is a difference in distribution. We can then conclude that patients that have breast cancer tumors with HR-deficiency responds to olaparib treatment.

The fisher.test() is two tailed as default setting.  
You can use fisher.test(aTable, alternative = “less”) or fisher.test(aTable, alternative = “greater”) for one-sided test.

<br><br><br>

## Starting from a dataset; Summarize data + Fisher

If you haven\`t summarized the 2x2 table yet, it is very handy to directly create the summary table from your raw data.

If you have your data in a tidy excel sheet, or csv file, you can read your data by

``` r
MyData <- readxl::read_xlsx("Path_to_your_file/Name_of_your_file.xlsx")
MyData <-read("Path_to_your_file/Name_of_your_file.csv")
```

But here we will make it from scratch, using the data from [supplementary table 4](https://www.annalsofoncology.org/cms/10.1016/j.annonc.2020.11.009/attachment/43ff8176-5b33-45f0-a5a2-12c08e4e96b0/mmc6.pdf) that was the raw data for the count table.

Where :  
CR= Complete response  
PR= Partial response  
SD= Stable Disease  
PD= Progressive disease

This is then dichotomized to responders (CR+PR) vs non-responders (SD+PD)

``` r
# Read vectors
Condition <- c("HR deficient", "HR proficient", "HR deficient", "HR deficient", "HR deficient", "HR deficient", "HR deficient", "HR deficient", "HR deficient", "HR proficient", "HR proficient", "HR deficient", "HR proficient", "HR deficient", "HR deficient", "HR proficient", "HR deficient","HR deficient", "HR deficient", "HR proficient","HR proficient", "HR proficient", "HR proficient", "HR proficient", "HR proficient", "HR deficient","HR deficient", "HR deficient","HR deficient","HR deficient","HR deficient","HR proficient")
Outcome <- c("Responder", "Non-Responder", "Responder","Responder","Non-Responder", "Responder", "Responder", "Responder", "Responder", "Non-Responder", "Responder", "Responder", "Non-Responder", "Responder", "Responder", "Responder", "Non-Responder","Non-Responder", "Responder", "Non-Responder", "Non-Responder", "Non-Responder", "Non-Responder", "Non-Responder", "Non-Responder", "Responder", "Responder", "Non-Responder", "Responder", "Responder", "Responder", "Non-Responder")

# Turn vectors into a date frame
MyData <- data.frame(Condition,Outcome)

# Optional:
# Turn characters into factor to set the order in which the different categories are listed in the summary table
MyData$Outcome <- factor(MyData$Outcome, levels = c("Responder","Non-Responder"))

# Print data frame
DT::datatable(MyData, options = list(searching = FALSE))
```

<div id="htmlwidget-1" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32"],["HR deficient","HR proficient","HR deficient","HR deficient","HR deficient","HR deficient","HR deficient","HR deficient","HR deficient","HR proficient","HR proficient","HR deficient","HR proficient","HR deficient","HR deficient","HR proficient","HR deficient","HR deficient","HR deficient","HR proficient","HR proficient","HR proficient","HR proficient","HR proficient","HR proficient","HR deficient","HR deficient","HR deficient","HR deficient","HR deficient","HR deficient","HR proficient"],["Responder","Non-Responder","Responder","Responder","Non-Responder","Responder","Responder","Responder","Responder","Non-Responder","Responder","Responder","Non-Responder","Responder","Responder","Responder","Non-Responder","Non-Responder","Responder","Non-Responder","Non-Responder","Non-Responder","Non-Responder","Non-Responder","Non-Responder","Responder","Responder","Non-Responder","Responder","Responder","Responder","Non-Responder"]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>Condition<\/th>\n      <th>Outcome<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"searching":false,"order":[],"autoWidth":false,"orderClasses":false,"columnDefs":[{"orderable":false,"targets":0}]}},"evals":[],"jsHooks":[]}</script>

Now that you have your data in the form of a data frame, you may:

1-Perform Fisher by columns inside the fisher.test() function call  
2-Make a summary table and perform Fisher on the table.

**1-Perform Fisher by columns Condition vs Outcome**

``` r
fisher.test(MyData$Condition, MyData$Outcome)
```

    ## 
    ##  Fisher's Exact Test for Count Data
    ## 
    ## data:  MyData$Condition and MyData$Outcome
    ## p-value = 0.0007899
    ## alternative hypothesis: true odds ratio is not equal to 1
    ## 95 percent confidence interval:
    ##    2.459807 228.297235
    ## sample estimates:
    ## odds ratio 
    ##   17.57049

**2-Make a summary table**

Summarize categorical data using the **table()** function:

``` r
anotherTable = table(MyData)
anotherTable
```

    ##                Outcome
    ## Condition       Responder Non-Responder
    ##   HR deficient         16             4
    ##   HR proficient         2            10

Then Perform Fisher using the summary table

``` r
fisher.test(anotherTable)
```

    ## 
    ##  Fisher's Exact Test for Count Data
    ## 
    ## data:  anotherTable
    ## p-value = 0.0007899
    ## alternative hypothesis: true odds ratio is not equal to 1
    ## 95 percent confidence interval:
    ##    2.459807 228.297235
    ## sample estimates:
    ## odds ratio 
    ##   17.57049

Does the two methods give the same result?

``` r
## Fetch value in the first list = p-value
PvalA = fisher.test(MyData$Condition, MyData$Outcome)[[1]]    
PvalB = fisher.test(anotherTable)[[1]] 

PvalA == PvalB
```

    ## [1] TRUE

**Yes they do!**

To get details on data and method used, i.e alternative = “two.sided,” assign the test results to an object and inspect the list elements in the R studio global environment.

``` r
getDetails = fisher.test(anotherTable)
```

or just type

``` r
unlist(fisher.test(anotherTable))
```

    ##                              p.value                            conf.int1 
    ##               "0.000789927616836742"                   "2.45980654095026" 
    ##                            conf.int2                  estimate.odds ratio 
    ##                   "228.297235459106"                   "17.5704859283499" 
    ##                null.value.odds ratio                          alternative 
    ##                                  "1"                          "two.sided" 
    ##                               method                            data.name 
    ## "Fisher's Exact Test for Count Data"                       "anotherTable"

Type ??fisher.test for more details on the method.
