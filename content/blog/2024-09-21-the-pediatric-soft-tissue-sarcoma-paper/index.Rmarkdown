---
title: The Pediatric Soft Tissue Sarcoma Paper, a COVID lock-down side quest and what is a genomic variant
author: Synnøve Yndestad
date: '2024-09-21'
slug: []
categories:
  - Clinical science
  - Genetics
  - Hereditary
  - COVID lock-down
tags:
  - DNA
  - VCF
  - NGS
subtitle: ''
excerpt: 'Our paper [**Germline variants in patients diagnosed with pediatric soft tissue sarcoma.**](https://pubmed.ncbi.nlm.nih.gov/39037077/) was published during the summer. 
This paper began with the story of a young male with a sarcoma in the prostate. This is a very rare condition. The treating physician wanted to find better treatment options and in an attempt to get some clues to what, our lab did 360 gene panel DNA sequencing to see if there was any targetable mutations that could direct the treatment of this young man.'
draft: no
comments: true
series: ~
layout: single
---



Our paper [**Germline variants in patients diagnosed with pediatric soft tissue sarcoma.**](https://pubmed.ncbi.nlm.nih.gov/39037077/) was published during the summer. 
This paper began with the story of a young male with a sarcoma in the prostate. This is a very rare condition. The treating physician wanted to find better treatment options and in an attempt to get some clues to what, our lab did 360 gene panel DNA sequencing to see if there was any targetable mutations that could direct the treatment of this young man. I unfortunately did not find any alterations that are known to predict response to a specific therapy. I did however discover a potentially damaging germline variant in a base excision repair gene, *CHD1L*. This gene has so far not been linked to hereditory cancer, but it is linked to congenital anomalies of the kidneys and urinary tract. Observing this potential link between the tumor site and birth defects was very intriguing. We hypothesized that low penetrance heritable defects in DNA repair genes could be responsible for the development of rhabdomyosarcoma in children and young adults. We were also wondering if potential deleterious variants was known for causing birth defects in the regions of the tumor site. Since this tumor type is so rare, it is challenging to identify enough cases for finding this kind of associations. This idea made our PI start having people looking for other cases. Digging in the clinical and pathological records from the hospital, they found 41 children and young adults (0–22 years) diagnosed with rhabdomyosarcoma, synovial sarcomas, and Ewing sarcomas between 1981 and 2019. 


![Main finidings](featured.jpg)

One of the things that surprised me the most about these pediatric sarcomas, was the diversity of locations and the diversity of germline variants. Whatever I found, it was always a case of n=1.  
Overall, I identified 7 pathogenic germline variants in these 41 children, while more than half was found to carry variants of uncertain significance. We had some of the usual suspects that are associated with cancer and sarcomas such as *TP53* and *DICER1*. Two of the pathogenic variants I found,  *MYO3A* and *MYO3B*, are not known to be associated with hereditary cancer. These genes are however important for cell polarity and shape. Functional loss of *MYO3A* and *MYO5B* can disrupt cell polarity and cause malformation of cochlea hairs and microvillus leading to deafness and microvillus inclusion disease. Since the patient with the *MYO3A* variant had a rhabdomyosarcoma in the soft palate, and the patient with the *MYO5B* in the pelvic area, I was even more intrigued by the possible tumor location and variant associations. These observations are only anecdotal, but perhaps in time more will be reported.  
Embryonal rhabdomyosarcomas has a peak incidence when the children are around 2 to 4 years, while the alveolar subtype, characterized by translocations between *PAX3* or *PAX7* and *FOXO1* genes, is diagnosed in children and young adults up to 20 years of age with no distinct age peak.

I belive that these pediatric tumors arises due to some kind of vulnerability during the growth spurs. They are so rare in adults, only 1% of cancers, while they account for more than 20% of the solid malignant cancers in children. This may be why the heritability in this tumor type is so hard to trace, due to low penetrance and a short window of vulnerability during growth spurs. 
In adult cancers, hereditary variants are mostly associated with DNA repair. If you have a faulty DNA repair gene such as *BRCA1*, if you live long enough, you will accumulate enough DNA damage and develop cancer.
This may not the be case in the pediatric sarcomas, and this is probably reflected in the wide variety in locations and germline variants. 





## COVID lockdown side quest

During the COVID lock-down of 2020 the lab was shut down, everyone was sent home for months not knowing when we would be able to return to work.    
My ongoing work in the cell-lab was shut down and never reopened.  
The RNA sequencing for my work on TNBC and PAPRP-inhibitors was set on hold. Not only due to the lock-down, but due to the logistic problems that came afterwards in the shipping sector. We faced problems we never before have dealt with, it was impossible to buy pipette tips for the lab. So we had to ration pipette tips, and even more delays followed.  
Now that my project was on hold and the world was set on pause, I could dive fullheartedly straight into this side quest. All the wet-lab was luckily finished before the lock-down, only data analyses remained.
So there I was, homeschooling my two kids while trying to figure out how to transform sequencing files into clinical meaningful information. Screening one sample for any targeteble variant that screams "Who´s bad!" is one thing, doing this systematically for 360 genes in 41 individuals is another ballgame.  
I needed another strategy.  


![COVID lockdown homeschooling 2](images/FunPic_20240921_211706606.jpg)


## What is a variant?

When we do NGS sequencing on gene panels with Illumina, we typically get a big pile of short reads of DNA sequences. These reads are compared to a "reference genome", and through bioinformatic workflows we puzzle those reads together to where they fit on the reference genome. We "map" and "align" every read. The number of times a region has been covered by a read is called "read depth".


![Finding variants](images/mapping.png)

[Image source](https://training.galaxyproject.org/training-material/topics/sequence-analysis/tutorials/mapping/tutorial.html)

 If there is a mismatch between the reference genome and the aligned sequence, it will be flagged as a variant. 
When sequencing tumor tissue, we want to go deep to be able to discover also the rare mutations in a tumor. We typically sequence by a 200x read depth with the 360 gene panel. When sequencing normal tissue it is not necessary to sequence that deep, because if a variant is present we expect it to either be present on one allele and 50% of all reads will have this variant, or if you inherited this variant from both your parents it will be present on both alleles and then a 100% of reads will have that variant.

![Finding variants](images/IGV_alignment_TP53.png)




## But, which variant is bad? How do we know?



For a variant in a gene to have any effect, it needs to affect the resulting protein. DNA nucleotides A, C, T and G are read in triplicates where each triplicate encodes for one amino acid.  
  
A **missense variant** is a variant where a nucleotide substitution leads to the replacement of one amino acid with another in the resulting protein. This change in amino acid sequence can potentially affect the protein's function.

A **nonsense variant** is a variant where a nucleotide substitution introduces a premature stop signal in the DNA sequence. This causes the protein-building process to halt early, resulting in a shortened or truncated protein that may be dysfunctional, improperly formed, or degraded by the cell.

An **intronic variant** is a variant that occurs within the non-coding regions (introns) of a gene. While introns are not directly involved in coding for proteins, changes in these regions can still affect gene function by interfering with processes like splicing, potentially leading to an improperly formed protein or altered gene regulation.  

**Insertions** and **deletions** of nucleotides are bigger events that has the potential to cause a lot of damage to the resulting protein. If even a single base is inserted or deleted, it can shift the reading frame so all the resulting amino acids added are wrong.  
  
  

How often a variant appears in a population will give us some idea if it is dangerous or not. If it is very common, it´s probably not dangerous. If it is very rare, it can be an indication that the variant might be damaging.  
  
  
Is the gene an oncogene that requires a specific activating mutation to be dangerous, or is it a tumor suppressor that only require to be inactivated by any means to be dangerous? Looking at the variant allele frequency in the tumor can give us some valuable information there.  
Consider a variant in an oncogene that stimulates growth signalling. Then it can be selected for in the tumor tissue, and you can find multiple copies of the variant in the tumor tissue.    
Consider a variant in a tumor suppressor.  Then a loss of the wild type healthy allele is selected for in the tumor tissue, and you get Loss Of Heterozygozity (LOH) in the tumor for that variant. LOH in tumors is kind of a trademark for tumor suppressors.  
And then we have all those genes that can be both a tumor suppressor and an oncogene depending on context, or we just don't know their exact function yet.

Then we must look at the computational evidence, the functional experimental evidence, and the clinical evidence. Automation and AI approaches for ACMG classification will get you partly there, but you still need a lot of hands-on evaluation afterwards.




![ACMG guidelines](images/gr1_lrgACMG_guidelines.jpeg)
[ACMG guidelines](https://www.gimjournal.org/article/S1098-3600(21)03031-8/fulltext#f0010)



Fast forward some years, and I have learned so much. Tools are created and abandoned in a corner of Github. 
New and improved pathogenicity scores appears, the ClinVar database is constantly evolving. This field does not stay still for you to finish quality checking all your called variants before your evaluation might be outdated due to new information becoming available. 
A variant of uncertain significance today, may be defined as pathogenic tomorrow. 
The number of theoretically possible variants in the human genome far outnumber the time we have available in a human lifespan to verify them experimentally. So the best we can do is often an educated guess by computational modelling. This uncertainty bothers me. I really want to know why those kids got cancer.





