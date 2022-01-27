---
title: How to 'Pivot Wider' when you have only character values
excerpt: "Reshaping your data by pivoting from long to wide, or wide to long is used frequently when wrangling your data.  
The pivot_longer() and pivot_wider() function from the [tidyr](https://tidyr.tidyverse.org/articles/pivot.html) package does this job excellent in most cases. I have however had some issues when reshaping data containing only characters.

This is my solution to this issue. "
author: Synnøve Yndestad
date: '2022-01-27'
slug: []
categories:
  - R
tags:
  - tidyverse
  - pivot_wider()
  - Categoricals
---



Reshaping your data by pivoting from long to wide, or wide to long is used frequently when wrangling your data.  
The pivot_longer() and pivot_wider() function from the [tidyr](https://tidyr.tidyverse.org/articles/pivot.html) package does this job excellent in most cases. I have however had some issues when reshaping data containing only characters.

This is my solution to this issue. 



```r
# Generate a long data frame
Subject <- c("Ken", "Ken", "Ken", "Abby","Abby", "Nick", "Nick","Nick","Nick","Rose")
Likes <- c("Cake", "Fish", "Mushroom", "Cake", "Pie", "Cake", "Mushroom", "Fish", "Pie", "Cake")
df <- as.data.frame(cbind(Subject, Likes))

df
```

```
##    Subject    Likes
## 1      Ken     Cake
## 2      Ken     Fish
## 3      Ken Mushroom
## 4     Abby     Cake
## 5     Abby      Pie
## 6     Nick     Cake
## 7     Nick Mushroom
## 8     Nick     Fish
## 9     Nick      Pie
## 10    Rose     Cake
```

Now we have a long data frame with types of food different people like. 

I want to "trick" pivot_longer() into pivoting my data even if I lack a value column. To do that, add a temporary value column  n = 1 

```r
# Add a value column in order to use pivot_wider()
df$n <- 1
df
```

```
##    Subject    Likes n
## 1      Ken     Cake 1
## 2      Ken     Fish 1
## 3      Ken Mushroom 1
## 4     Abby     Cake 1
## 5     Abby      Pie 1
## 6     Nick     Cake 1
## 7     Nick Mushroom 1
## 8     Nick     Fish 1
## 9     Nick      Pie 1
## 10    Rose     Cake 1
```
Then we can pivot wider easily.


```r
library(tidyverse)
df.w <- pivot_wider(df, id_cols = Subject,    # Specify id-column to keep
                        names_from = Likes,   # Column characters that go to column names
                        values_from = n)      # Value 

df.w
```

```
## # A tibble: 4 × 5
##   Subject  Cake  Fish Mushroom   Pie
##   <chr>   <dbl> <dbl>    <dbl> <dbl>
## 1 Ken         1     1        1    NA
## 2 Abby        1    NA       NA     1
## 3 Nick        1     1        1     1
## 4 Rose        1    NA       NA    NA
```


Now we see that everybody likes cake, but this is not the output we wanted. 
Time to replace the temporary "1" values with characters.

**We have the hard way:**  
Rename the value = 1 for each column individually

```r
# The hard way, not applicable to other datasets
df.w$Cake <- str_replace(df.w$Cake, "1", "Cake")
df.w$Fish <- str_replace(df.w$Fish, "1", "Fish")
df.w$Mushroom <- str_replace(df.w$Mushroom, "1", "Mushroom")
df.w$Pie <- str_replace(df.w$Pie, "1", "Pie")
```

**And the easy way:**  
A more generic way to do this is to make a function that will iterate over each column and replace the value with the respective column name.


```r
# Search for "1", replace with column names
for (i in 1:length(df.w)) {
  df.w[[i]] <- str_replace(df.w[[i]], "1", colnames(df.w)[i])
}

df.w
```

```
## # A tibble: 4 × 5
##   Subject Cake  Fish  Mushroom Pie  
##   <chr>   <chr> <chr> <chr>    <chr>
## 1 Ken     Cake  Fish  Mushroom <NA> 
## 2 Abby    Cake  <NA>  <NA>     Pie  
## 3 Nick    Cake  Fish  Mushroom Pie  
## 4 Rose    Cake  <NA>  <NA>     <NA>
```

Neat!


Does this also works backwards? Yes it does!


**Go from Wide to Long:**  


```r
# Search for column names, replace with "1"
for (i in 1:length(df.w)) {
  df.w[[i]] <- str_replace(df.w[[i]], colnames(df.w)[i], "1")   
}

# Pivot longer
df.l = df.w %>% pivot_longer(!Subject,                   # Select all but Subject columns to pivot
                             names_to = "Likes",         # Create the "Likes" column
                             values_to = "n",            # The value column
                             values_drop_na = TRUE) %>%  # Remember to drop the NA rows
                select(-n)                               # Remove the "n" column

df.l 
```

```
## # A tibble: 10 × 2
##    Subject Likes   
##    <chr>   <chr>   
##  1 Ken     Cake    
##  2 Ken     Fish    
##  3 Ken     Mushroom
##  4 Abby    Cake    
##  5 Abby    Pie     
##  6 Nick    Cake    
##  7 Nick    Fish    
##  8 Nick    Mushroom
##  9 Nick    Pie     
## 10 Rose    Cake
```

Now we are back where we started, a long data frame containing only character values.




