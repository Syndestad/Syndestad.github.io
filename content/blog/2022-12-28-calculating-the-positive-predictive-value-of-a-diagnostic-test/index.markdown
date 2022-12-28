---
title: Calculating the positive predictive value of a diagnostic test
author: Synnøve Yndestad
date: '2022-12-28'
slug: []
categories:
  - Clinical science
  - Statistics
  - R
tags:
  - caret
  - broom
  - positive predictive value
  - sensitivity
  - accuracy
  - confusionMatrix()
subtitle: ''
excerpt: 'Will a diagnostic test have the same predictive value regardless of how it is used? My intuitive answer is "It should", but hey, this is why we do statistics before implementing large screening programs. The predictive value of a test is different when it is used in a high-risk than when it is used in a low-risk population. It is also different if the test is used as a screening test versus when it is used as a confirmatory test, or used in two populations with different prevalence. Sensitivity, specificity and accuracy are very important principles to consider when evaluating how and when diagnostic tests should be used, such as the mammography screening program, or when evaluating the differences in policies regarding COVID testing during the early and late stages of the pandemic.'
draft: no
series: ~
layout: single
---



Will a diagnostic test have the same predictive value regardless of how it is used? My intuitive answer is "It should", but hey, this is why we do statistics before implementing large screening programs. The predictive value of a test is different when it is used in a high-risk than when it is used in a low-risk population. It is also different if the test is used as a screening test versus when it is used as a confirmatory test, or used in two populations with different prevalence. Sensitivity, specificity and accuracy are very important principles to consider when evaluating how and when diagnostic tests should be used, such as the mammography screening program, or when evaluating the differences in policies regarding COVID testing during the early and late stages of the pandemic.

## Some definitions:

![<https://www.reddit.com/r/Mcat/comments/ao6ovi/type_i_and_type_ii_errors/>](TypeIandIIError.png)

For a test to have a reliable predictive value, we want the number of false positives and the number of false negatives to be as low as possible. Both can have serious implications, where a missed diagnosis might cause a patient to go untreated for a cancer that could have been curable. While a false positive result may give rise to unnecessary treatment with unnecessary side effectes, anxiety and stress.

The Null hypothesis is that there is no real effect. False positive (α) occurs when we incorrectly reject the null hypothesis, while a false negative (β) is incorrectly accepting the null hypothesis.

**Some definitions:**

Pr = probability

We can calculate 

Sensitivity = Pr(positive test \| disease)\
Specificity = pr(negative test \| no disease)\
Positive predictive value = Pr( disease \| positive test)\
Negative predictive value = Pr( no disease \| negative test)\
Accuracy = Pr( correct outcome)

*In other words:*\
Sensitivity = TP/(TP+FN)\
Specificity = TN/(FP+TN)\
Positive predictive value = TP/(TP+FP)\
Negative predictive value = TN/(FN+TN)\
Accuracy = (TP+TN)/(TP+FP+FN+TN)

## Calculate positive predictive value in two populations with different prevalence

Assume we have a disease with 0.1% prevalence. To detect the disease, we have a kit that test for the disease that has a 99% sensitivity and 99% specificity.

To get a positive result, what is the probability of a person having the disease if they are drawn from a:  

1 -Randomly selected population with 0.1% prevalence\
2 -A high risk population with 10% prevalence

Generate the two TP/FP/TN/FP matrixes:


```r
library(tidyverse)
```


```r
# A TP/FP/TN/FP matrix from a random population with 0.1% prevalence:
Random = data.frame(
        # Create a vector of the test results for each sample, 
        # enter matrix by row
          test = c(rep("+", 99), rep("+", 999), 
                    rep("-", 1), rep("-", 98901)),
         # Create a vector of the disease status for each sample, 
         # enter matrix by row
          disease = c(rep("+", 99), rep("-", 999), 
                       rep("+", 1), rep("-", 98901))
            
           )
# Set factor levels to get "+" first in the table
Random$test = factor(Random$test, levels = c("+", "-")) 
Random$disease = factor(Random$disease, levels = c("+", "-"))

# Create the table using the table() function
Random_tbl = table(Random)
```


```r
# A TP/FP/TN/FP matrix for a high risk sub-population with 10% prevalence:
HighRisk = data.frame(
        # Create a vector of the test results for each sample, 
        # enter matrix by row
          test = c(rep("+", 9900), rep("+", 900), 
                    rep("-", 100), rep("-", 89100)),
         # Create a vector of the disease status for each sample, 
         # enter matrix by row
          disease = c(rep("+", 9900), rep("-", 900), 
                       rep("+", 100), rep("-", 89100))

           )
# Set factor levels to get "+" first in the table
HighRisk$test = factor(HighRisk$test, levels = c("+", "-")) 
HighRisk$disease = factor(HighRisk$disease, levels = c("+", "-"))

# Create the table using the table() function
HighRisk_tbl = table(HighRisk)
```


So, when using the test with 99% sensitivity and 99% specificity, what is the positive predictive value in the two populations?

**The TP/FP/TN/FP matrix from the random population:**


```r
Random_tbl
```

```
##     disease
## test     +     -
##    +    99   999
##    -     1 98901
```

Sensitivity = 99/(99+1) = 99%  
Specificity = 98901/(999+98901) = 99%  
Positive predictive value = 99/ (99+999) = around 9%  
Negative predictive value = 98901/(1+98901) \> 99.9%  
Accuracy = (99+98901)/100000 = 99%.

The test results from the random population is bit unbalanced because the condition is so rare. In the random population the test has only 9% positive predictive value.\
Even though the test specificity is really high, the positive predictive value is quite low.

**The TP/FP/TN/FP matrix from the high risk population:**


```r
HighRisk_tbl
```

```
##     disease
## test     +     -
##    +  9900   900
##    -   100 89100
```

Sensitivity = 9900/(9900+100) = 99%  
Specificity = 89100/(900+89100) = 99%  
Positive predictive value = 9900/ (9900+900) =around 92%  
Negative preddictive value = 89100/(100+89100) =around 99.9%  
Accuracy = (9900+89100)/100000 = 99%

Now the positive predictive value is a lot higher, around 92%. Note that the sensitivity and accuracy is equal. This shows that tests have a higher positive predictive value when used in high risk populations, or in populations with a higher prevalence.

This principle applies to i.e the mammography screening program that does not screen the entire female population, but only the age group or people with a family history that makes them more high risk. It also applies to changes in policies around COVID testing of the general population vs only the people with symptoms during the different stages of the pandemic with a high/low prevalence of disease.

## Using R to calculate positive predictive value.

We can use the **caret** R package to calculate everything calculated manually above by using the `confusionMatrix()` function. If we also use the **broom** package, we can get the test results as tidy tibbles.

Test results from the random population:


```r
library(caret)
## Tidy results
Random_stat = broom::tidy(confusionMatrix(Random_tbl, positive="+"))
## Original results
confusionMatrix(Random_tbl, positive="+")
```

```
## Confusion Matrix and Statistics
## 
##     disease
## test     +     -
##    +    99   999
##    -     1 98901
##                                           
##                Accuracy : 0.99            
##                  95% CI : (0.9894, 0.9906)
##     No Information Rate : 0.999           
##     P-Value [Acc > NIR] : 1               
##                                           
##                   Kappa : 0.1637          
##                                           
##  Mcnemar's Test P-Value : <2e-16          
##                                           
##             Sensitivity : 0.99000         
##             Specificity : 0.99000         
##          Pos Pred Value : 0.09016         
##          Neg Pred Value : 0.99999         
##              Prevalence : 0.00100         
##          Detection Rate : 0.00099         
##    Detection Prevalence : 0.01098         
##       Balanced Accuracy : 0.99000         
##                                           
##        'Positive' Class : +               
## 
```

Test results from the high-risk population:


```r
library(caret)
## Tidy results
HighRisk_stat = broom::tidy(confusionMatrix(HighRisk_tbl, positive="+"))
## Original results
confusionMatrix(HighRisk_tbl, positive="+")
```

```
## Confusion Matrix and Statistics
## 
##     disease
## test     +     -
##    +  9900   900
##    -   100 89100
##                                           
##                Accuracy : 0.99            
##                  95% CI : (0.9894, 0.9906)
##     No Information Rate : 0.9             
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9464          
##                                           
##  Mcnemar's Test P-Value : < 2.2e-16       
##                                           
##             Sensitivity : 0.9900          
##             Specificity : 0.9900          
##          Pos Pred Value : 0.9167          
##          Neg Pred Value : 0.9989          
##              Prevalence : 0.1000          
##          Detection Rate : 0.0990          
##    Detection Prevalence : 0.1080          
##       Balanced Accuracy : 0.9900          
##                                           
##        'Positive' Class : +               
## 
```


The tidy output is in the form of a tibble:

```r
HighRisk_stat
```

```
## # A tibble: 14 × 6
##    term                 class estimate conf.low conf.high    p.value
##    <chr>                <chr>    <dbl>    <dbl>     <dbl>      <dbl>
##  1 accuracy             <NA>     0.99     0.989     0.991  0        
##  2 kappa                <NA>     0.946   NA        NA     NA        
##  3 mcnemar              <NA>    NA       NA        NA      7.44e-141
##  4 sensitivity          +        0.99    NA        NA     NA        
##  5 specificity          +        0.99    NA        NA     NA        
##  6 pos_pred_value       +        0.917   NA        NA     NA        
##  7 neg_pred_value       +        0.999   NA        NA     NA        
##  8 precision            +        0.917   NA        NA     NA        
##  9 recall               +        0.99    NA        NA     NA        
## 10 f1                   +        0.952   NA        NA     NA        
## 11 prevalence           +        0.1     NA        NA     NA        
## 12 detection_rate       +        0.099   NA        NA     NA        
## 13 detection_prevalence +        0.108   NA        NA     NA        
## 14 balanced_accuracy    +        0.99    NA        NA     NA
```


Then, fetch only the positive predictive values from the tidy results in the two populations and combine them in a table:


```r
Random_stat %>% filter(term == "pos_pred_value") %>% 
    mutate(Population = "Random") %>% 
    select(term, estimate, Population) %>% 
  
   rbind(
HighRisk_stat %>% filter(term == "pos_pred_value") %>% 
   mutate(Population = "High risk") %>% 
   select(term, estimate, Population)
   ) %>% 
  knitr::kable()
```



|term           |  estimate|Population |
|:--------------|---------:|:----------|
|pos_pred_value | 0.0901639|Random     |
|pos_pred_value | 0.9166667|High risk  |



