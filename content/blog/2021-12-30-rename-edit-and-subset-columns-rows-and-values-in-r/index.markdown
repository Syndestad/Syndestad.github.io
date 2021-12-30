---
title: Rename, edit and subset columns, rows and values in R
excerpt: "A 'How to' add and edit single variables in a data frame. Rename and edit column and row names, and subset rows."
author: Synnøve Yndestad
date: '2021-12-30'
slug: []
categories:
  - R
tags:
  - tidyverse
  - subsetting
  - edit
---



How does one add or edit single variables in a data frame?    
This is a "how to" rename and edit column names, row names and single values in R.  
Then how to subset rows by filtering.



First, lets make a data frame of gene names with Entrez ID, HGNC symbols, and ENSMBL ID that we can edit.


```r
# Add vectors
Ens <- c("ENSG00000142208", "ENSG00000012048", "ENSG00000139618", "ENSG00000091831", "ENSG00000121879", "ENSG00000171862", "ENSG00000141510")
hg <- c("AKT1", "BRCA1", "BRCA2", "ESR1", "PIK3CA", "PTEN", "TP53")
entr <- c("207", "672", "675", "2099", "5290", "5728", "7157")
expr <- c(NA, NA, "Low", "Medium", "Medium", "Low", "High")

#  use cbind to bind the vectors as columns, and return as data frame
df <- as.data.frame(cbind(Ens, hg, entr, expr))
df
```

```
##               Ens     hg entr   expr
## 1 ENSG00000142208   AKT1  207   <NA>
## 2 ENSG00000012048  BRCA1  672   <NA>
## 3 ENSG00000139618  BRCA2  675    Low
## 4 ENSG00000091831   ESR1 2099 Medium
## 5 ENSG00000121879 PIK3CA 5290 Medium
## 6 ENSG00000171862   PTEN 5728    Low
## 7 ENSG00000141510   TP53 7157   High
```




## Changing Column names

To change single column names, you can either change by column number, or by column name.  

**Change name of by number:**  

Changing the name of column 2:  
`colnames(df)[2] <- "newname2"`  
  
When working with larger data sets, you don´t always know the exact location of the column.   
To change name by column names:    
  
`names(df)[names(df) == "old.name"] <- "new.name"`  


Step by step, this will do the following;  
  
**names(df)**  
Check all the names in the data frame = df  
  
  
**[names(df) == "old.name"]**  
Extract the name you want to check   
  
  
**<- "new.name"**                   
Assign the new  name.  




```r
# Change column name by number
colnames(df)[2] <- "new.name.1"

# Change column name by column name
names(df)[names(df) == "Ens"] <- 'new.name.2'

df
```

```
##        new.name.2 new.name.1 entr   expr
## 1 ENSG00000142208       AKT1  207   <NA>
## 2 ENSG00000012048      BRCA1  672   <NA>
## 3 ENSG00000139618      BRCA2  675    Low
## 4 ENSG00000091831       ESR1 2099 Medium
## 5 ENSG00000121879     PIK3CA 5290 Medium
## 6 ENSG00000171862       PTEN 5728    Low
## 7 ENSG00000141510       TP53 7157   High
```



To change **all column names**, we can use a vector.


```r
MyColumnNames <- c("ENSMBL","HGNC_symbol", "ENTREZ", "Expression")

colnames(df) <- MyColumnNames

df
```

```
##            ENSMBL HGNC_symbol ENTREZ Expression
## 1 ENSG00000142208        AKT1    207       <NA>
## 2 ENSG00000012048       BRCA1    672       <NA>
## 3 ENSG00000139618       BRCA2    675        Low
## 4 ENSG00000091831        ESR1   2099     Medium
## 5 ENSG00000121879      PIK3CA   5290     Medium
## 6 ENSG00000171862        PTEN   5728        Low
## 7 ENSG00000141510        TP53   7157       High
```


## Changing Row Names

Use row.names() and a vector to change all row names.

We can turn the Entrez-ID column into row names by:


```r
row.names(df) <- df$ENTREZ

df
```

```
##               ENSMBL HGNC_symbol ENTREZ Expression
## 207  ENSG00000142208        AKT1    207       <NA>
## 672  ENSG00000012048       BRCA1    672       <NA>
## 675  ENSG00000139618       BRCA2    675        Low
## 2099 ENSG00000091831        ESR1   2099     Medium
## 5290 ENSG00000121879      PIK3CA   5290     Medium
## 5728 ENSG00000171862        PTEN   5728        Low
## 7157 ENSG00000141510        TP53   7157       High
```



## Editing single values:


The syntax for editing single values in a data frame is:

`data.frame[row_number, column_number] = new_value`  
  
or  
  
`data.frame["row_name", "column_name"] = new_value`  
  
Then, if we got new data or need to change a value, we can add it to the data frame.    

The expression values (High, Medium, Low) for AKT1 is located in row number 1, column number 4. To edit the expression value for AKT1, select [1, 4].
You can also use the row- and column names. In order to change the expression value for BRCA1 from "NA" to Low, we select the row name and column name by ["672", "Expression"].  


```r
#  Edit single value using
## [Row number, Column number]
df[1, 4]= "High"

## ["Row Name", "Column Name"]
df["672", "Expression"] = "Low"
```



Similarly, we can add a new row using the same syntax:

```r
# Add new row by Row-name
df["1019", ] = c("ENSG00000135446", NA, NA, "Medium")
df
```

```
##               ENSMBL HGNC_symbol ENTREZ Expression
## 207  ENSG00000142208        AKT1    207       High
## 672  ENSG00000012048       BRCA1    672        Low
## 675  ENSG00000139618       BRCA2    675        Low
## 2099 ENSG00000091831        ESR1   2099     Medium
## 5290 ENSG00000121879      PIK3CA   5290     Medium
## 5728 ENSG00000171862        PTEN   5728        Low
## 7157 ENSG00000141510        TP53   7157       High
## 1019 ENSG00000135446        <NA>   <NA>     Medium
```

If the data.frame is big and we don´t know the row-name, we have other options.

Change single values by extracting the column and select the value that defines the row

`data.frame$column_to_change[data.frame$Column_value_to_search_for=="Value_key"] <- "Value_to_change"`



```r
# Change hgnc_symbol from "NA" to "CDK4"
df$HGNC_symbol[df$ENSMBL=="ENSG00000135446"] <- "CDK4"

# Change entrezid for gene CDK4 from "NA" to "1019"
df$ENTREZ[df$ENSMBL=="ENSG00000135446"] <- "1019"
 

df
```

```
##               ENSMBL HGNC_symbol ENTREZ Expression
## 207  ENSG00000142208        AKT1    207       High
## 672  ENSG00000012048       BRCA1    672        Low
## 675  ENSG00000139618       BRCA2    675        Low
## 2099 ENSG00000091831        ESR1   2099     Medium
## 5290 ENSG00000121879      PIK3CA   5290     Medium
## 5728 ENSG00000171862        PTEN   5728        Low
## 7157 ENSG00000141510        TP53   7157       High
## 1019 ENSG00000135446        CDK4   1019     Medium
```


## Adding rows

There are many ways to add new rows.


```r
# Add an empty row to a data frame
df[nrow(df)+1,] <- NA

# Add new empty row by Row-name
df["NewRowName", ] <- NA

# Add new empty row by Row-number
df[11, ] <- NA

df
```

```
##                     ENSMBL HGNC_symbol ENTREZ Expression
## 207        ENSG00000142208        AKT1    207       High
## 672        ENSG00000012048       BRCA1    672        Low
## 675        ENSG00000139618       BRCA2    675        Low
## 2099       ENSG00000091831        ESR1   2099     Medium
## 5290       ENSG00000121879      PIK3CA   5290     Medium
## 5728       ENSG00000171862        PTEN   5728        Low
## 7157       ENSG00000141510        TP53   7157       High
## 1019       ENSG00000135446        CDK4   1019     Medium
## 9                     <NA>        <NA>   <NA>       <NA>
## NewRowName            <NA>        <NA>   <NA>       <NA>
## 11                    <NA>        <NA>   <NA>       <NA>
```


## Subsetting data frames

Tidyverse is a collection of R packages that work well together and is very useful for transforming and visualizing data.
The dplyr::filter() function is particularly useful when subsetting data frames. 


```r
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──
```

```
## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
## ✓ tibble  3.1.6     ✓ dplyr   1.0.7
## ✓ tidyr   1.1.4     ✓ stringr 1.4.0
## ✓ readr   2.1.1     ✓ forcats 0.5.1
```

```
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```


## Subsetting Rows

Subset the top 3 rows.
Remember the syntax [row, column]

```r
# Top 3 observations
Top3 <- df[1:3,]
Top3
```

```
##              ENSMBL HGNC_symbol ENTREZ Expression
## 207 ENSG00000142208        AKT1    207       High
## 672 ENSG00000012048       BRCA1    672        Low
## 675 ENSG00000139618       BRCA2    675        Low
```

Subset the genes with low expression  

```r
# Select rows with a value equal to ==
LowExpression = filter(df, Expression == "Low")
LowExpression
```

```
##               ENSMBL HGNC_symbol ENTREZ Expression
## 672  ENSG00000012048       BRCA1    672        Low
## 675  ENSG00000139618       BRCA2    675        Low
## 5728 ENSG00000171862        PTEN   5728        Low
```

Find any rows that contains a specific string, i.e "BRCA"

```r
# Select rows that contains a specific string
filter(df, str_detect(HGNC_symbol, "BRCA"))
```

```
##              ENSMBL HGNC_symbol ENTREZ Expression
## 672 ENSG00000012048       BRCA1    672        Low
## 675 ENSG00000139618       BRCA2    675        Low
```



## Removing/excluding Rows


```r
# Removing one or more rows using a vector of row numbers. use i.e c(3,6,9) etc to select rows 3, 6 and 9.
df <- df[-c(9),]

# Removing a row using row names
df <- df[row.names(df) != "NewRowName", , drop = FALSE]

# Remove a row using dplyr::filter() and != which means not equal to
df <- filter(df, HGNC_symbol != "AKT1")

df
```

```
##               ENSMBL HGNC_symbol ENTREZ Expression
## 672  ENSG00000012048       BRCA1    672        Low
## 675  ENSG00000139618       BRCA2    675        Low
## 2099 ENSG00000091831        ESR1   2099     Medium
## 5290 ENSG00000121879      PIK3CA   5290     Medium
## 5728 ENSG00000171862        PTEN   5728        Low
## 7157 ENSG00000141510        TP53   7157       High
## 1019 ENSG00000135446        CDK4   1019     Medium
```




## Subset rows with multiple conditionsn 

Using base R:  


```r
subset(df, Expression=="Low" & HGNC_symbol == "BRCA1")
```

```
##              ENSMBL HGNC_symbol ENTREZ Expression
## 672 ENSG00000012048       BRCA1    672        Low
```

Using tidyverse grammar:  

```r
df %>% filter(Expression == "Low" & 
              HGNC_symbol == "BRCA1")
```

```
##              ENSMBL HGNC_symbol ENTREZ Expression
## 672 ENSG00000012048       BRCA1    672        Low
```






## Removing rows with NA

Missing values may cause problems in downstream analysis, and knowing how to deal with them is important.
Sometimes, we need to exclude all rows with missing values.


```r
# Add a row with missing values
df["NewRowName", ] <- NA
df
```

```
##                     ENSMBL HGNC_symbol ENTREZ Expression
## 672        ENSG00000012048       BRCA1    672        Low
## 675        ENSG00000139618       BRCA2    675        Low
## 2099       ENSG00000091831        ESR1   2099     Medium
## 5290       ENSG00000121879      PIK3CA   5290     Medium
## 5728       ENSG00000171862        PTEN   5728        Low
## 7157       ENSG00000141510        TP53   7157       High
## 1019       ENSG00000135446        CDK4   1019     Medium
## NewRowName            <NA>        <NA>   <NA>       <NA>
```

Remove all rows with missing values using na.omit()

```r
df <- na.omit(df)
df
```

```
##               ENSMBL HGNC_symbol ENTREZ Expression
## 672  ENSG00000012048       BRCA1    672        Low
## 675  ENSG00000139618       BRCA2    675        Low
## 2099 ENSG00000091831        ESR1   2099     Medium
## 5290 ENSG00000121879      PIK3CA   5290     Medium
## 5728 ENSG00000171862        PTEN   5728        Low
## 7157 ENSG00000141510        TP53   7157       High
## 1019 ENSG00000135446        CDK4   1019     Medium
```













