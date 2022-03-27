---
title: Extract tables from pdf files with tabulizer
author: synnøve yndestad
date: '2022-03-27'
slug: []
categories:
  - R
tags:
  - webscraping
  - pdf
  - data.frame()
  - tidyverse
  - tabulizer
subtitle: 'Tabulizer, from pdf-url to R data.frame()!'
excerpt: 'Far too often i find myself in a situation where I need to fetch lists of genes, expression data or similar from journal articles, only to to realize that the data is only to be found buried somewhere deep within the supplementary in the form of a giant pdf. (The horror!)  Here is a how to to scrape data from a linked pdf file (by url) using the tabulizer R package.'
---

Far too often i find myself in a situation where I need to fetch lists of genes, expression data or similar from journal articles, only to to realize that the data is only to be found buried somewhere deep within the supplementary in the form of a giant pdf.  
(The horror!)  
If you really want people to use your stuff and cite your work, Please stop doing that. Just add a csv file with the data. Make it accessible. The reason for publishing your data is for others to be able to use it, right? Then stop making it so hard for us to access it! 

Now, after getting this off my chest, here is a how to scrape data from a pdf file (by url) using the tabulizer R package.



Install tabulizer from CRAN.


```r
if (!require("remotes")) {
    install.packages("remotes")
}
# on 64-bit Windows
remotes::install_github(c("ropensci/tabulizerjars", "ropensci/tabulizer"), INSTALL_opts = "--no-multiarch")
# elsewhere
remotes::install_github(c("ropensci/tabulizerjars", "ropensci/tabulizer"))
```




Load packages.


```r
library(tabulizer)
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──
```

```
## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
## ✓ tibble  3.1.6     ✓ dplyr   1.0.8
## ✓ tidyr   1.2.0     ✓ stringr 1.4.0
## ✓ readr   2.1.2     ✓ forcats 0.5.1
```

```
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```



Specify the url to where the file is located, and download it. We want [this pdf file]("https://static-content.springer.com/esm/art%3A10.1038%2Fncomms4361/MediaObjects/41467_2014_BFncomms4361_MOESM760_ESM.pdf") 


```r
url = "https://static-content.springer.com/esm/art%3A10.1038%2Fncomms4361/MediaObjects/41467_2014_BFncomms4361_MOESM760_ESM.pdf"
download.file(url,"41467_2014_BFncomms4361_MOESM760_ESM.pdf",mode="wb")
```

We want the table named 'Supplementary Table 1: The list of 230 genes designated as “the HRD gene signature"'. The table is located over pages 22-26.


Extract the table from pages 22-26 with extract_tables()  
This produces one list pr page.  


```r
HRDlist = extract_tables("41467_2014_BFncomms4361_MOESM760_ESM.pdf", pages = 22:26)

glimpse(HRDlist)
```

```
## List of 5
##  $ : chr [1:44, 1:4] "Gene Symbol" "AADAT" "ADM" "AHSA1" ...
##  $ : chr [1:47, 1:4] "CDC7" "CDCA2" "CDCA3" "CDCA5" ...
##  $ : chr [1:47, 1:4] "FHOD3" "FLRT3" "FOXO3" "FXYD3" ...
##  $ : chr [1:47, 1:4] "METTL3" "MLF1IP" "MLKL" "MME" ...
##  $ : chr [1:46, 1:4] "RNASEH2A" "RRAGD" "RRM2" "SDCBP2" ...
```

Take a look at the content of the first list:


```r
head(HRDlist[[1]])
```

```
##      [,1]          [,2]                                             
## [1,] "Gene Symbol" "Gene expression of\rBRCA1-shRNA/Control-\rshRNA"
## [2,] "AADAT"       "0.471246673"                                    
## [3,] "ADM"         "3.507136497"                                    
## [4,] "AHSA1"       "0.291138066"                                    
## [5,] "ALDH3B1"     "5.644456865"                                    
## [6,] "ALDH6A1"     "3.236368847"                                    
##      [,3]                                            
## [1,] "Gene expression of Rad51-\rshRNA/Control-shRNA"
## [2,] "0.427892763"                                   
## [3,] "5.103009422"                                   
## [4,] "0.461385918"                                   
## [5,] "3.777395591"                                   
## [6,] "3.79262783"                                    
##      [,4]                                             
## [1,] "Gene expression of\rBRIT1-shRNA/Control-\rshRNA"
## [2,] "0.432906783"                                    
## [3,] "2.790489783"                                    
## [4,] "0.296262701"                                    
## [5,] "7.888853059"                                    
## [6,] "5.167951941"
```




Bind the lists together and turn into data frame


```r
HRDsignature = do.call(rbind, HRDlist) %>% 
               as.data.frame()
glimpse(HRDsignature)
```

```
## Rows: 231
## Columns: 4
## $ V1 <chr> "Gene Symbol", "AADAT", "ADM", "AHSA1", "ALDH3B1", "ALDH6A1", "ALG8…
## $ V2 <chr> "Gene expression of\rBRCA1-shRNA/Control-\rshRNA", "0.471246673", "…
## $ V3 <chr> "Gene expression of Rad51-\rshRNA/Control-shRNA", "0.427892763", "5…
## $ V4 <chr> "Gene expression of\rBRIT1-shRNA/Control-\rshRNA", "0.432906783", "…
```

```r
head(HRDsignature)
```

```
##            V1                                              V2
## 1 Gene Symbol Gene expression of\rBRCA1-shRNA/Control-\rshRNA
## 2       AADAT                                     0.471246673
## 3         ADM                                     3.507136497
## 4       AHSA1                                     0.291138066
## 5     ALDH3B1                                     5.644456865
## 6     ALDH6A1                                     3.236368847
##                                               V3
## 1 Gene expression of Rad51-\rshRNA/Control-shRNA
## 2                                    0.427892763
## 3                                    5.103009422
## 4                                    0.461385918
## 5                                    3.777395591
## 6                                     3.79262783
##                                                V4
## 1 Gene expression of\rBRIT1-shRNA/Control-\rshRNA
## 2                                     0.432906783
## 3                                     2.790489783
## 4                                     0.296262701
## 5                                     7.888853059
## 6                                     5.167951941
```

Some cleanup is required to get correct column names.


```r
# Turn first row into column names
HRDsignature[1,]
```

```
##            V1                                              V2
## 1 Gene Symbol Gene expression of\rBRCA1-shRNA/Control-\rshRNA
##                                               V3
## 1 Gene expression of Rad51-\rshRNA/Control-shRNA
##                                                V4
## 1 Gene expression of\rBRIT1-shRNA/Control-\rshRNA
```

```r
colnames(HRDsignature) = HRDsignature[1,]

# remove first row
HRDsignature = HRDsignature[-1,]

glimpse(HRDsignature)
```

```
## Rows: 230
## Columns: 4
## $ `Gene Symbol`                                     <chr> "AADAT", "ADM", "AHS…
## $ `Gene expression of\rBRCA1-shRNA/Control-\rshRNA` <chr> "0.471246673", "3.50…
## $ `Gene expression of Rad51-\rshRNA/Control-shRNA`  <chr> "0.427892763", "5.10…
## $ `Gene expression of\rBRIT1-shRNA/Control-\rshRNA` <chr> "0.432906783", "2.79…
```

```r
head(HRDsignature)
```

```
##   Gene Symbol Gene expression of\rBRCA1-shRNA/Control-\rshRNA
## 2       AADAT                                     0.471246673
## 3         ADM                                     3.507136497
## 4       AHSA1                                     0.291138066
## 5     ALDH3B1                                     5.644456865
## 6     ALDH6A1                                     3.236368847
## 7        ALG8                                     0.459931384
##   Gene expression of Rad51-\rshRNA/Control-shRNA
## 2                                    0.427892763
## 3                                    5.103009422
## 4                                    0.461385918
## 5                                    3.777395591
## 6                                     3.79262783
## 7                                    0.397772753
##   Gene expression of\rBRIT1-shRNA/Control-\rshRNA
## 2                                     0.432906783
## 3                                     2.790489783
## 4                                     0.296262701
## 5                                     7.888853059
## 6                                     5.167951941
## 7                                      0.34249794
```

Now we have converted a 4 page pdf table into a data.frame ready to use!  
This workflow can now be implemented into scripts, linking directly to the source data for reproducibility.
Happy pdf scraping!  

