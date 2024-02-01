---
title: 'Digital PD-L1 CPS score: A workflow for Positive cell detection and classification
  using QuPath'
author: Synnøve Yndestad
date: '2024-01-21'
slug: []
excerpt: "Identifying patients that can benefit from specific drugs is essential in cancer treatment.
The KEYNOTE 355 trial established a survival benefit for patients with metastatic Triple Negative Breast Cancer (TNBC) receiving the PD-L1 targeting drug pembrolizumab in patients with a PD-L1 Combined Positive Score (CPS) >= 10.
CPS score is calculated by counting the number of PD-L1 positive tumor cells, the number of PD-L1 positive immune cells, divided by the total number of tumor cells. Manually counting positive and
negative cells is time consuming and may be prone to variability. Using digital pathology to estimate CPS score can increase precision, accuracy and reproducibility. It may also offer the opportunity of saving time by batch processing a large number of slides.
Calculating CPS score digitally requires a workflow that can detect positive and negative stained cells, as well as identifying tumor cells and immune cells, while ignoring stromal cells and artefacts. This can be achieved by training a classifier, which then can be applied for batch processing of digital slides. Here I present a workflow for how to calculate CPS score using QuPath as performed in our recent paper3. QuPath is an open-source digital pathology platform designed for the analysis of histopathological images. QuPath supports a range of image analysis tasks, making it a valuable tool in the field of pathology and biomedical research."
categories:
  - IHC
  - QuPath
  - Digital pathology
tags: []
---


Identifying patients that can benefit from specific drugs is essential in cancer treatment.
The KEYNOTE 355 trial established a survival benefit for patients with metastatic Triple Negative Breast Cancer (TNBC) receiving the PD-L1 targeting drug pembrolizumab in patients with a PD-L1 Combined Positive Score (CPS) >= 10.
CPS score is calculated by counting the number of PD-L1 positive tumor cells, the number of PD-L1 positive immune cells, divided by the total number of tumor cells. Manually counting positive and
negative cells is time consuming and may be prone to variability. Using digital pathology to estimate CPS score can increase precision, accuracy and reproducibility. It may also offer the opportunity of saving time by batch processing a large number of slides.
Calculating CPS score digitally requires a workflow that can detect positive and negative stained cells, as well as identifying tumor cells and immune cells, while ignoring stromal cells and artefacts. This can be achieved by training a classifier, which then can be applied for batch processing of digital slides. Here I present a workflow for how to calculate CPS score using QuPath as performed in our recent paper3. QuPath is an open-source digital pathology platform designed for the analysis of histopathological images. QuPath supports a range of image analysis tasks, making it a valuable tool in the field of pathology and biomedical research.





![Poster](/slides/PDL1_CPS_score_poster_Yndestad_MIMV_2023.pdf)





Results from the PD-L1 score is presented in  [**Homologous Recombination Deficiency Across Subtypes of Primary Breast Cancer**](https://pubmed.ncbi.nlm.nih.gov/38039432/) Yndestad et.al JCO Precis Oncol 2023.
 7: e2300338.  

This poster was presented in "Bringing AI and Precision Medicine to Patient Care", The Sixth Annual MMIV Conference Bergen (2023). 




