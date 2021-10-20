---
title: An intro to biomaRt
author: Synnøve Yndestad
date: '2021-10-19'
slug: []
categories: ["R", "Bioinformatics", "sequencing"]
tags: ["biomaRt", "gene annotation", "gene ontology", "dbSNP", "ClinVar", "ensembl", "entrez", "hgnc", "database query"]
---

<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>

Or “How to annotate Hugo Symbol and Entrez ID to ensembl ID, fetch the location of the genes and associated gene ontologies and dbSNPs using biomaRt.”  
biomaRt is a Bioconductor package that provides an R interface to the HGNC BioMart server. It makes it possible to access and query a large amount of data and resources from ensembl. We can use that for gene annotation and database mining across multiple species.

Genes and transcripts are named in a variety of ways, which can be quite confusing and a source of errors.  
The ensembl identifiers is based on the NCBI RefSeq set and is stable. They are consistent and unambiguous across releases. Gene names on the other hand may change based on new knowledge or preferences, but the stable identifiers will remain the same.
Working with ensembl format id´s is therefore more reproducible and consistent over time, but we will need to translate those to more human recognizable format like hugo symbol (HGNC) gene names at the end stage of the data processing. Many bioinformatic tools also requires entrez id, i.e various subtyping tools. This means that translating between ensembl id, hugo symbol and entrez id is something that we need to do quite frequently, and depending on your workflow.

## Code in brief

Just show me the code, no explanation needed:

``` r
library(biomaRt)
ensembl <- useMart("ensembl", dataset = "hsapiens_gene_ensembl")

Values <- c("ENSG00000142208", "ENSG00000132781", "ENSG00000139618", "ENSG00000012048", "ENSG00000121879", "ENSG00000073050","ENSG00000181555", "ENSG00000141510")

MyGenes <- getBM(attributes = c("ensembl_gene_id", "hgnc_symbol", "entrezgene_id"), 
                 filters = "ensembl_gene_id",
                 values = Values,
                 mart = ensembl)
MyGenes
```

    ##   ensembl_gene_id hgnc_symbol entrezgene_id
    ## 1 ENSG00000012048       BRCA1           672
    ## 2 ENSG00000073050       XRCC1          7515
    ## 3 ENSG00000121879      PIK3CA          5290
    ## 4 ENSG00000132781       MUTYH          4595
    ## 5 ENSG00000139618       BRCA2           675
    ## 6 ENSG00000141510        TP53          7157
    ## 7 ENSG00000142208        AKT1           207
    ## 8 ENSG00000181555       SETD2         29072

## Getting started; Select database and dataset

Install from bioconductor for R Version 4.1:

``` r
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("biomaRt")
```

Load package and list the available BioMart databases.

``` r
library(biomaRt)
library(tidyverse)

# List the available BioMart databases 
listMarts()
```

    ##                biomart                version
    ## 1 ENSEMBL_MART_ENSEMBL      Ensembl Genes 104
    ## 2   ENSEMBL_MART_MOUSE      Mouse strains 104
    ## 3     ENSEMBL_MART_SNP  Ensembl Variation 104
    ## 4 ENSEMBL_MART_FUNCGEN Ensembl Regulation 104

We want the ensembl BioMart, useMart to select and save it as mart.

``` r
# Pick the ensembl database and save it
mart <- useMart("ensembl")
mart
```

    ## Object of class 'Mart':
    ##   Using the ENSEMBL_MART_ENSEMBL BioMart database
    ##   No dataset selected.

Each database contains several datasets.  
Have a look:

``` r
head(listDatasets(mart))
```

    ##                        dataset                           description
    ## 1 abrachyrhynchus_gene_ensembl Pink-footed goose genes (ASM259213v1)
    ## 2     acalliptera_gene_ensembl      Eastern happy genes (fAstCal1.2)
    ## 3   acarolinensis_gene_ensembl       Green anole genes (AnoCar2.0v2)
    ## 4    acchrysaetos_gene_ensembl       Golden eagle genes (bAquChr1.2)
    ## 5    acitrinellus_gene_ensembl        Midas cichlid genes (Midas_v5)
    ## 6    amelanoleuca_gene_ensembl       Giant panda genes (ASM200744v2)
    ##       version
    ## 1 ASM259213v1
    ## 2  fAstCal1.2
    ## 3 AnoCar2.0v2
    ## 4  bAquChr1.2
    ## 5    Midas_v5
    ## 6 ASM200744v2

Search for the Human dataset

``` r
listDatasets(mart) %>% filter(grepl("Human", description))
```

    ##                 dataset              description    version
    ## 1 hsapiens_gene_ensembl Human genes (GRCh38.p13) GRCh38.p13

This tells us that the current version of the Human genes is GRCh38.p13.  
Make a note of the version used and the date you downloaded the dataset. If an older version for some reason is required, it is possible to make a query especially for the version you need.

Now, select the Homo Sapiens dataset.

``` r
# Select your dataset
ensembl <- useDataset("hsapiens_gene_ensembl", mart)
ensembl
```

    ## Object of class 'Mart':
    ##   Using the ENSEMBL_MART_ENSEMBL BioMart database
    ##   Using the hsapiens_gene_ensembl dataset

## Build the query

To build your query, we use values, attributes, filters and which mart.

Filters = The kind of value you have, and want to search against (i.e “ensembl\_gene\_id”)  
Attributes = The stuff you want to get, i.e hugo symbols, entrez id´s etc.  
Values = The input values to annotate (Specific id\`s, like "ENSG00000132781)

Get the available filters and attributes with listFilters() and listAttributes() and store them in your environment for easier inspection.

``` r
# Fetch available filters
filters <- listFilters(ensembl)
head(filters)
```

    ##              name              description
    ## 1 chromosome_name Chromosome/scaffold name
    ## 2           start                    Start
    ## 3             end                      End
    ## 4      band_start               Band Start
    ## 5        band_end                 Band End
    ## 6    marker_start             Marker Start

``` r
# Fetch available attributes
attributes <- listAttributes(ensembl)
head(attributes)
```

    ##                            name                  description         page
    ## 1               ensembl_gene_id               Gene stable ID feature_page
    ## 2       ensembl_gene_id_version       Gene stable ID version feature_page
    ## 3         ensembl_transcript_id         Transcript stable ID feature_page
    ## 4 ensembl_transcript_id_version Transcript stable ID version feature_page
    ## 5            ensembl_peptide_id            Protein stable ID feature_page
    ## 6    ensembl_peptide_id_version    Protein stable ID version feature_page

In the attributes, we find the all the attributes we can use in the query; “ensembl\_gene\_id,” “hgnc\_symbol” and “entrezgene\_id”

To get the entrez id and hgnc symbol for **all** ensembl id´s, we don´t need any values. We can just call getBM() with the attributes we want:

``` r
All.ensembl.ids <- getBM(attributes = c("ensembl_gene_id", "hgnc_symbol", "entrezgene_id"), mart = ensembl)
head(All.ensembl.ids)
```

    ##   ensembl_gene_id hgnc_symbol entrezgene_id
    ## 1 ENSG00000210049       MT-TF            NA
    ## 2 ENSG00000211459     MT-RNR1            NA
    ## 3 ENSG00000210077       MT-TV            NA
    ## 4 ENSG00000210082     MT-RNR2            NA
    ## 5 ENSG00000209082      MT-TL1            NA
    ## 6 ENSG00000198888      MT-ND1          4535

``` r
# Print the number of rows
nrow(All.ensembl.ids)
```

    ## [1] 67485

This may be saved as a key for future reference and consistent workflows. Take note of the chromosome version used and the date it was downloaded for future references.

``` r
write_csv(All.ensembl.ids, "Ensembl_hgnc_entrez_key_GRCh38p13_191021.csv")
```

All the genes are more than we need for most purposes. In stead, we build a query containing only our genes of interest.
Now, take the ensembl id´s that you want to annotate into a vector to use in the getBM() query as values, and set “ensembl\_gene\_id” as filters to specify that your values are of the type “ensembl\_gene\_id.”

``` r
GenesOfInterest <- c("ENSG00000142208", "ENSG00000132781", "ENSG00000139618", "ENSG00000012048", "ENSG00000121879", "ENSG00000073050","ENSG00000181555", "ENSG00000141510")

MyGenes <- getBM(attributes = c("ensembl_gene_id", "hgnc_symbol", "entrezgene_id"), 
                 filters = "ensembl_gene_id",
                 values = GenesOfInterest,
                 mart = ensembl)
DT::datatable(MyGenes)
```

<div id="htmlwidget-1" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"filter":"none","vertical":false,"data":[["1","2","3","4","5","6","7","8"],["ENSG00000012048","ENSG00000073050","ENSG00000121879","ENSG00000132781","ENSG00000139618","ENSG00000141510","ENSG00000142208","ENSG00000181555"],["BRCA1","XRCC1","PIK3CA","MUTYH","BRCA2","TP53","AKT1","SETD2"],[672,7515,5290,4595,675,7157,207,29072]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>ensembl_gene_id<\/th>\n      <th>hgnc_symbol<\/th>\n      <th>entrezgene_id<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":3},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

Similarly, we can fetch where our genes of interests are located by chromosome, start position, end position and band. We use the Values vector as before, but now we don´t include the ensembl\_gene\_id or the entrez\_id in the output (the attributes to list) since we don´t need that in this output:

``` r
Location <- getBM(attributes = c("hgnc_symbol", "chromosome_name", 'start_position', 'end_position', 'band'), 
                 filters = "ensembl_gene_id",
                 values = GenesOfInterest,
                 mart = ensembl)
DT::datatable(Location)
```

<div id="htmlwidget-2" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-2">{"x":{"filter":"none","vertical":false,"data":[["1","2","3","4","5","6","7","8"],["BRCA1","XRCC1","PIK3CA","MUTYH","BRCA2","TP53","AKT1","SETD2"],[17,19,3,1,13,17,14,3],[43044295,43543311,179148114,45329163,32315086,7661779,104769349,47016429],[43170245,43580473,179240093,45340893,32400268,7687538,104795751,47164113],["q21.31","q13.31","q26.32","p34.1","q13.1","p13.1","q32.33","p21.31"]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>hgnc_symbol<\/th>\n      <th>chromosome_name<\/th>\n      <th>start_position<\/th>\n      <th>end_position<\/th>\n      <th>band<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[2,3,4]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

Then we can answer things like, which genes are the shortest/longest

``` r
DT::datatable(Location %>% mutate(Length = end_position-start_position) %>% 
                           arrange(Length))
```

<div id="htmlwidget-3" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-3">{"x":{"filter":"none","vertical":false,"data":[["1","2","3","4","5","6","7","8"],["MUTYH","TP53","AKT1","XRCC1","BRCA2","PIK3CA","BRCA1","SETD2"],[1,17,14,19,13,3,17,3],[45329163,7661779,104769349,43543311,32315086,179148114,43044295,47016429],[45340893,7687538,104795751,43580473,32400268,179240093,43170245,47164113],["p34.1","p13.1","q32.33","q13.31","q13.1","q26.32","q21.31","p21.31"],[11730,25759,26402,37162,85182,91979,125950,147684]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>hgnc_symbol<\/th>\n      <th>chromosome_name<\/th>\n      <th>start_position<\/th>\n      <th>end_position<\/th>\n      <th>band<\/th>\n      <th>Length<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[2,3,4,6]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

## Search for GO terms

Now we want to see if any of our genes of interest are associated with a particular DNA repair pathway. Then we can fetch the genes associated with the GO terms for Base Excision Repair, Double Strand Break, MisMatch Repair and Single Strand Break repair.

BER: GO:0006284  
DSB: GO:0006302  
MMR: GO:0006298  
SSB: GO:0000012

``` r
BER.Genes <- getBM(attributes= c("hgnc_symbol"),
                   filters=c("go"),
                   values = "GO:0006284",
                   mart=ensembl)
DSB.Genes <- getBM(attributes= c("hgnc_symbol"),
                   filters=c("go"),
                   values = "GO:0006302",
                   mart=ensembl)
MMR.Genes <- getBM(attributes= c("hgnc_symbol"),
                   filters=c("go"),
                   values = "GO:0006298",
                   mart=ensembl)
SSB.genes <- getBM(attributes= c("hgnc_symbol"),
                   filters=c("go"),
                   values = "GO:0000012",
                   mart=ensembl)
```

Merge the results:

``` r
BER.Genes$BER <- "BER"
DSB.Genes$DSB <- "DSB"
MMR.Genes$MMR <- "MMR"
SSB.genes$SSB <- "SSB"

RepairGenes <- BER.Genes %>% full_join(DSB.Genes) %>% 
                             full_join(MMR.Genes) %>% 
                             full_join(SSB.genes)  
```

    ## Joining, by = "hgnc_symbol"
    ## Joining, by = "hgnc_symbol"
    ## Joining, by = "hgnc_symbol"

``` r
# Add annotation to MyResults
MyGenes <- MyGenes %>% left_join(RepairGenes)
```

    ## Joining, by = "hgnc_symbol"

``` r
DT::datatable(MyGenes)
```

<div id="htmlwidget-4" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-4">{"x":{"filter":"none","vertical":false,"data":[["1","2","3","4","5","6","7","8"],["ENSG00000012048","ENSG00000073050","ENSG00000121879","ENSG00000132781","ENSG00000139618","ENSG00000141510","ENSG00000142208","ENSG00000181555"],["BRCA1","XRCC1","PIK3CA","MUTYH","BRCA2","TP53","AKT1","SETD2"],[672,7515,5290,4595,675,7157,207,29072],[null,"BER",null,"BER",null,null,null,null],["DSB",null,null,null,"DSB","DSB",null,null],[null,null,null,"MMR",null,null,null,"MMR"],[null,"SSB",null,null,null,null,null,null]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>ensembl_gene_id<\/th>\n      <th>hgnc_symbol<\/th>\n      <th>entrezgene_id<\/th>\n      <th>BER<\/th>\n      <th>DSB<\/th>\n      <th>MMR<\/th>\n      <th>SSB<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":3},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

## Fetch dbSNP from biomaRt

We can fetch variants from dbSNP, but then we need to query a different mart, the “snp” mart.

`snpmart = useEnsembl(biomart = "snp", dataset="hsapiens_snp")`

``` r
table(attributes$page)
```

    ## 
    ## feature_page     homologs    sequences          snp  snp_somatic    structure 
    ##          203         2782           60           44           35           40

To find what attributes we can use in the query, we can search in the snp attributes for the ensembl mart we created in the beginning.

``` r
attributes %>% filter(page == "snp")
```

    ##                             name                             description page
    ## 1                ensembl_gene_id                          Gene stable ID  snp
    ## 2        ensembl_gene_id_version                  Gene stable ID version  snp
    ## 3                        version                          Version (gene)  snp
    ## 4          ensembl_transcript_id                    Transcript stable ID  snp
    ## 5  ensembl_transcript_id_version            Transcript stable ID version  snp
    ## 6             transcript_version                    Version (transcript)  snp
    ## 7             ensembl_peptide_id                       Protein stable ID  snp
    ## 8     ensembl_peptide_id_version               Protein stable ID version  snp
    ## 9                peptide_version                       Version (protein)  snp
    ## 10               chromosome_name                Chromosome/scaffold name  snp
    ## 11                start_position                         Gene start (bp)  snp
    ## 12                  end_position                           Gene end (bp)  snp
    ## 13                        strand                                  Strand  snp
    ## 14                          band                          Karyotype band  snp
    ## 15            external_gene_name                               Gene name  snp
    ## 16          external_gene_source                     Source of gene name  snp
    ## 17              transcript_count                        Transcript count  snp
    ## 18    percentage_gene_gc_content                       Gene % GC content  snp
    ## 19                   description                        Gene description  snp
    ## 20                variation_name                            Variant name  snp
    ## 21    germ_line_variation_source                          Variant source  snp
    ## 22            source_description              Variant source description  snp
    ## 23                        allele                         Variant alleles  snp
    ## 24                     validated             Variant supporting evidence  snp
    ## 25                     mapweight                               Mapweight  snp
    ## 26                  minor_allele                            Minor allele  snp
    ## 27             minor_allele_freq                  Minor allele frequency  snp
    ## 28            minor_allele_count                      Minor allele count  snp
    ## 29         clinical_significance                   Clinical significance  snp
    ## 30           transcript_location                Transcript location (bp)  snp
    ## 31         snp_chromosome_strand               Variant chromosome Strand  snp
    ## 32              peptide_location                   Protein location (aa)  snp
    ## 33              chromosome_start chromosome/scaffold position start (bp)  snp
    ## 34                chromosome_end   Chromosome/scaffold position end (bp)  snp
    ## 35      polyphen_prediction_2076                     PolyPhen prediction  snp
    ## 36           polyphen_score_2076                          PolyPhen score  snp
    ## 37          sift_prediction_2076                         SIFT prediction  snp
    ## 38               sift_score_2076                              SIFT score  snp
    ## 39   distance_to_transcript_2076                  Distance to transcript  snp
    ## 40                cds_start_2076                               CDS start  snp
    ## 41                  cds_end_2076                                 CDS end  snp
    ## 42                 peptide_shift                          Protein allele  snp
    ## 43             synonymous_status                     Variant consequence  snp
    ## 44            allele_string_2076             Consequence specific allele  snp

So, to get all the known snp´s for MUTYH, we can use the chromosome location in the query above to select the snp´s to extract. We specify in the filters and values the location to list SNPs from. So we specify the location og MUTYH by chromosome name, start position and end position.

From the Location table above:  
MUTYH, 1, 45329163, 45340893

Get all known variants in MUTYH and print the variants that is listed as pathogenic in clinical\_significance.

``` r
# Load the snp mart:
snpmart = useEnsembl(biomart = "snp", dataset="hsapiens_snp")


MUTYH_snp <-getBM(attributes = c('refsnp_id','allele','chrom_start',"clinical_significance",
                                 "minor_allele_freq","validated"), 
                     filters = c('chr_name','start','end'), 
                      values = list(1,  45329163,   45340893), 
                        mart = snpmart)

DT::datatable(MUTYH_snp %>% filter(grepl("pathogenic", clinical_significance)))
```

<div id="htmlwidget-5" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-5">{"x":{"filter":"none","vertical":false,"data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57","58","59","60","61","62","63","64","65","66","67","68","69","70","71","72","73","74","75","76","77","78","79","80","81","82","83","84","85","86","87","88","89","90","91","92","93","94","95","96","97","98","99","100","101","102","103","104","105","106","107","108","109","110","111","112","113","114","115","116","117","118","119","120","121","122","123","124","125","126","127","128","129","130","131","132","133","134","135","136","137","138","139","140","141","142","143","144","145","146","147","148","149","150","151","152","153","154","155","156"],["rs878854186","rs587782716","rs1553123017","rs1570314279","rs876659420","rs1557451154","rs932830392","rs1064793198","rs587782228","rs1057517459","rs1570346782","rs864621967","rs863224502","rs876660774","rs146331482","rs1553124893","rs587778541","rs121908381","rs376790729","rs200844166","rs1553125075","rs1553125100","rs1570366181","rs1553125243","rs876659414","rs876660837","rs1064795480","rs1553125622","rs1553125677","rs1060501325","rs1553125766","rs1060501321","rs1570373385","rs1437789978","rs121908383","rs766420907","rs1553125914","rs587780078","rs863224501","rs1570376147","rs529008617","rs121908382","rs886039606","rs36053993","rs587781628","rs587781337","rs1064795219","rs587783057","rs1060501335","rs587778536","rs745910470","rs1114167685","rs869312771","rs768130289","rs1057517456","rs1060501324","rs1553126848","rs587780082","rs558173961","rs776362892","rs587781338","rs587780751","rs1060501333","rs1553127514","rs730881834","rs879254257","rs374950566","rs1553127659","rs761468459","rs730881833","rs876659676","rs769237459","rs1553127825","rs786203115","rs1338038953","rs1570406302","rs773087549","rs587782885","rs587780749","rs140342925","rs200495564","rs1060501346","rs34126013","rs1064793197","rs1057517765","rs1570409700","rs1557474906","rs878854193","rs199989617","rs1064796630","rs1570415363","rs376561094","rs745921592","rs1553128663","rs1553128712","rs766553845","rs1553128813","rs750592289","rs587781864","rs143353451","rs747993448","rs34612342","rs1057517457","rs1570423722","rs1306473047","rs1553129062","rs786203161","rs781222233","rs587782730","rs1570428456","rs777184451","rs762307622","rs1553129349","rs876660787","rs1057520660","rs1553129521","rs876660190","rs1570433022","rs876659625","rs1553129638","rs1553129652","rs864622450","rs1553129676","rs587781295","rs730881832","rs372267274","rs863224452","rs786203213","rs1553129892","rs1553130042","rs888691362","rs878854189","rs1557485553","rs765123255","rs121908380","rs748170941","rs1553130185","rs1557486313","rs138775799","rs1570443585","rs558707786","rs370124822","rs746449748","rs1557487793","rs587782066","rs1330675110","rs1060501336","rs587781704","rs768386527","rs1570465624","rs587780088","rs1570467133","rs1383826978","rs1553136984","rs767402084","rs1064795596"],["T/-","GC/CT","CAC/CACAC","GGAG/GGAGGGAG","C/A","ATACAGGT/TGGGCTGTTGG","G/A","A/C","C/A/T","C/T","CT/-","TAGTGCCT/T","T/A","T/A","GG/G/GGGG","T/TT","TCCTCCT/TCCT","C/A","C/A/T","G/T","GTG/G","T/TT","CCC/CC","T/-","GAGAGA/GAGA","C/A","CCCC/CCC","C/-","GGCCCAGCCC/-","C/T","TTC/G","C/A","CC/C","G/A","T/C","G/A","CTC/CTCTC","CCC/CCCCC","GGTCACGGACGGG/GG","GG/G","G/A","G/A","CCTGCCAGCAGACCTGAGAGGGAGGGCAGCCAGGCAGGGGTCAGGCCT/CCT","C/A/T","T/C","C/A/T","C/T","G/A","A/G","GGG/GG","G/A","A/T","GGCAGAGCTCTCCTCCCTGGG/GG","GGGGGG/GGGG/GGGGG/GGGGGGG","T/-","C/T","CAGGGCTCCGAGGGAGGCAGGCACAGG/CAGG","G/A","G/A/T","CCC/CC","G/A","T/G","A/C","CTCTCT/CTCT","G/C","C/A/T","G/A","G/A/C","CCCC/CCC","C/A/T","T/C","G/A","A/CT","G/A","C/T","C/T","G/A/T","G/A/C","C/A/T","C/T","G/A","C/T","G/A","GTGCTA/AGCAGCTG","T/C/G","C/T","C/T","C/A","C/G/T","G/A","AGCTGTGTAGCGCCCCACGCCAGGCAGGAGCTG/AGCTG","G/A","C/A","T/C","CC/C","CCTTCCGAGCTCCCTCCT/CCT","G/A","G/A","C/T","C/A/G/T","G/A","T/C","AGCCCAGGCCAGCCCAG/AGCCCAG","C/T","C/A/T","CC/C","T/C/G","CCTATTTCCCCTACC/CC","A/C/G","GTA/TT","G/A","C/A/T","C/-","T/A","C/G/T","A/C","ATCCAT/ATCCATATCCAT","G/C","G/A","GGG/GG","TGACCTCTGAGACC/TGACCTCTGAGACCTGACCTCTGAGACC","T/A","CCC/CC","C/T","A/T","C/G/T","T/C","C/-","C/A/T","C/A/T","C/T","T/TT","A/G","G/A","G/A/T","C/G/T","C/T","CCC/CC","G/A","TT/TTT","T/C","G/A/C","CC/C","C/A","T/C","C/T","TT/T","C/-","G/A","CTTCCTGTGACCACTTCCCACGGCTGCTCGTGGCTTCCTCATGATGGCCTGAAACAAAAAGACCCAGCCAAAGCAGTCAGTCACAATGAGGCCAAATTTTGAGGCCTTCCAAGGGTGATAGCTATCTCCCTCTCATCCACCAT/G","G/A/C","T/C","T/C","C/T","C/G/T","C/G/T"],[45329404,45329405,45329406,45329409,45330515,45330517,45330533,45330543,45330557,45330558,45330558,45331176,45331184,45331187,45331197,45331211,45331219,45331220,45331223,45331240,45331255,45331263,45331287,45331302,45331316,45331335,45331422,45331446,45331454,45331462,45331474,45331476,45331476,45331479,45331502,45331503,45331513,45331515,45331519,45331525,45331529,45331530,45331544,45331556,45331558,45331660,45331661,45331676,45331684,45331700,45331712,45331722,45331728,45331746,45331769,45331800,45331801,45331835,45332049,45332057,45332080,45332163,45332164,45332165,45332181,45332209,45332215,45332222,45332240,45332242,45332252,45332279,45332290,45332300,45332310,45332391,45332401,45332440,45332443,45332445,45332446,45332457,45332458,45332458,45332466,45332473,45332489,45332573,45332574,45332576,45332604,45332636,45332678,45332689,45332762,45332762,45332780,45332786,45332791,45332794,45332795,45332803,45332804,45332817,45332818,45332831,45332836,45332887,45332916,45332949,45332952,45332955,45332957,45332959,45332960,45333095,45333101,45333115,45333138,45333138,45333153,45333158,45333165,45333166,45333168,45333171,45333172,45333315,45333321,45333412,45333423,45333428,45333429,45333436,45333449,45333452,45333453,45333467,45333472,45333485,45333507,45333513,45333561,45333583,45333605,45334390,45334446,45334457,45334463,45334464,45334493,45334505,45334513,45340218,45340219,45340220],["likely pathogenic","pathogenic","likely pathogenic","likely pathogenic","uncertain significance,likely pathogenic","pathogenic","uncertain significance,likely pathogenic","likely pathogenic","uncertain significance,likely pathogenic,pathogenic","likely pathogenic","likely pathogenic","likely pathogenic","pathogenic","likely pathogenic","pathogenic","pathogenic","not provided,pathogenic","pathogenic","uncertain significance,likely pathogenic,pathogenic","pathogenic","pathogenic","likely pathogenic","pathogenic","pathogenic","likely pathogenic","likely pathogenic","likely pathogenic,pathogenic","pathogenic","pathogenic","pathogenic","likely pathogenic","pathogenic","pathogenic","pathogenic","pathogenic","pathogenic","likely pathogenic","likely pathogenic,pathogenic","uncertain significance,likely pathogenic,pathogenic","pathogenic","pathogenic","pathogenic","likely pathogenic,pathogenic","uncertain significance,likely pathogenic,pathogenic","likely pathogenic,pathogenic","likely pathogenic,pathogenic","likely pathogenic","pathogenic","likely pathogenic,pathogenic","uncertain significance,not provided,likely pathogenic,pathogenic","pathogenic","pathogenic","pathogenic","likely pathogenic,pathogenic","likely pathogenic,pathogenic","likely pathogenic,pathogenic","pathogenic","pathogenic","uncertain significance,pathogenic","pathogenic","pathogenic","uncertain significance,pathogenic","likely pathogenic","likely pathogenic","uncertain significance,likely pathogenic","uncertain significance,likely pathogenic","likely pathogenic,pathogenic","uncertain significance,pathogenic","likely pathogenic,pathogenic","uncertain significance,likely pathogenic,pathogenic","likely pathogenic,pathogenic","likely pathogenic","pathogenic","pathogenic","pathogenic","pathogenic","uncertain significance,likely pathogenic,pathogenic","uncertain significance,likely pathogenic,pathogenic","uncertain significance,likely pathogenic","likely pathogenic,pathogenic","likely pathogenic,pathogenic","likely pathogenic","likely pathogenic,pathogenic","pathogenic","uncertain significance,likely pathogenic,pathogenic","likely pathogenic","likely pathogenic","likely pathogenic","likely pathogenic,pathogenic","likely pathogenic,pathogenic","pathogenic","pathogenic","pathogenic","likely pathogenic","likely pathogenic","uncertain significance,likely pathogenic","likely pathogenic,pathogenic","uncertain significance,likely pathogenic","uncertain significance,likely pathogenic,pathogenic","uncertain significance,likely pathogenic,pathogenic","likely pathogenic,pathogenic","likely pathogenic,pathogenic","likely pathogenic,pathogenic","pathogenic","uncertain significance,pathogenic","likely pathogenic,pathogenic","likely pathogenic","likely pathogenic,pathogenic","likely pathogenic,pathogenic","pathogenic","uncertain significance,pathogenic","uncertain significance,likely pathogenic,pathogenic","likely pathogenic","pathogenic","likely pathogenic,pathogenic","likely pathogenic","likely pathogenic,pathogenic","pathogenic","pathogenic","pathogenic","pathogenic","uncertain significance,likely pathogenic","pathogenic","likely pathogenic,pathogenic","likely pathogenic,pathogenic","likely pathogenic,pathogenic","pathogenic","uncertain significance,pathogenic","uncertain significance,likely pathogenic,pathogenic","likely pathogenic","likely pathogenic,pathogenic","pathogenic","likely pathogenic","likely pathogenic,pathogenic","uncertain significance,benign,likely benign,pathogenic","uncertain significance,pathogenic","pathogenic","pathogenic","pathogenic","pathogenic","uncertain significance,likely pathogenic","uncertain significance,likely pathogenic,pathogenic","likely pathogenic,pathogenic","pathogenic","uncertain significance,likely pathogenic","likely pathogenic","pathogenic","pathogenic","likely pathogenic,pathogenic","pathogenic","uncertain significance,pathogenic","pathogenic","uncertain significance,likely pathogenic","likely pathogenic","pathogenic","uncertain significance,likely pathogenic,pathogenic"],[null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,0.001797,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,0.0002,null,null,0.002396,null,null,null,null,null,null,null,null,null,null,null,null,null,null,0.0002,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,0.0002,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,0.0002,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,0.000599,null,null,null,null,null,0.0002,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null],["Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Frequency,Phenotype_or_Disease,TOPMed","Frequency,Phenotype_or_Disease","1000Genomes,Cited,Frequency,Phenotype_or_Disease","Phenotype_or_Disease","Cited,ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","1000Genomes,Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease","ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Frequency,gnomAD,Phenotype_or_Disease","Cited,Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Frequency,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","1000Genomes,Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Cited,Phenotype_or_Disease","Phenotype_or_Disease","1000Genomes,Cited,ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","Cited,ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","ExAC,Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","Cited,Phenotype_or_Disease","ExAC,Frequency,Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","1000Genomes,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Frequency,gnomAD,Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Cited,Phenotype_or_Disease","Cited,ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Cited,ExAC,Frequency,Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Frequency,gnomAD,Phenotype_or_Disease,TOPMed","ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Frequency,gnomAD,Phenotype_or_Disease","Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","ExAC,Frequency,gnomAD,Phenotype_or_Disease","Cited,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Cited,ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","1000Genomes,Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Frequency,gnomAD,Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Cited,Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Phenotype_or_Disease","ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease","ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","1000Genomes,Cited,ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Frequency,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Frequency,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Frequency,gnomAD,Phenotype_or_Disease","ExAC,Frequency,gnomAD,Phenotype_or_Disease","ExAC,Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","ExAC,Frequency,gnomAD,Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Frequency,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Frequency,Phenotype_or_Disease,TOPMed","ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Cited,ESP,Frequency,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Frequency,Phenotype_or_Disease","Phenotype_or_Disease","Phenotype_or_Disease","Frequency,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","1000Genomes,Cited,ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Phenotype_or_Disease","ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","1000Genomes,ExAC,Frequency,gnomAD,Phenotype_or_Disease","ESP,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Frequency,Phenotype_or_Disease","Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","ExAC,Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease","Cited,ExAC,Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","Frequency,gnomAD,Phenotype_or_Disease,TOPMed","Phenotype_or_Disease","ExAC,Frequency,gnomAD,Phenotype_or_Disease","Phenotype_or_Disease"]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>refsnp_id<\/th>\n      <th>allele<\/th>\n      <th>chrom_start<\/th>\n      <th>clinical_significance<\/th>\n      <th>minor_allele_freq<\/th>\n      <th>validated<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[3,5]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

Other things you can do with biomaRt is to retrieve gene sequences, coding regions, 5’UTR regions, transcripts, protein sequence etc using the getSequence() function.

For more details on how to query in biomaRt see the excellent [vignette](https://bioconductor.org/packages/release/bioc/vignettes/biomaRt/inst/doc/accessing_ensembl.html) or [reference manual](https://bioconductor.org/packages/release/bioc/html/biomaRt.html).
