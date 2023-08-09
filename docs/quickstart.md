---
title: Quick start
description: >-
    Quick and easy NextITS instruction for impatient people
---

## Quick start  

Get started quickly with NextITS by following these simple instructions.  

The pipeline is divided into two main steps:  
1. The first step is executed separately for each sequencing run because it involves the removal of tag-jumps. This step primarily focuses on sequence quality control, ITS extraction, and chimera removal.  
2. The second step combines data from various runs, clusters sequences using either vsearch or swarm, and then performs post-clustering curation.  

##  Pull the latest pipeline version

``` bash
nextflow pull vmikk/NextITS
```


## Step 1

As a first step, each sequencing run must be processed individually.  

!!! info "DRY principle"
    For clarity, we're providing separate commands below. 
    Note, however, that this approach doesn't adhere to the DRY (Don't Repeat Yourself) principle and may lead to errors. 
    To minimize potential errors, you can encapsulate the command within a function (for details, see below).


Sequencing run #1:  
``` bash
nextflow run vmikk/NextITS -r main \
  -profile singularity \
  -resume \
  --input          "$(pwd)/Run01/Run01.fastq.gz" \
  --barcodes       "$(pwd)/Run01/Run01_barcodes.fasta" \
  --primer_forward "GTACACACCGCCCGTCG" \
  --primer_reverse "CCTSCSCTTANTDATATGC" \
  --outdir         "Step1_Results/Run01"
```

Sequencing run #2:  
``` bash
nextflow run vmikk/NextITS -r main \
  -profile singularity \
  -resume \
  --input          "$(pwd)/Run02/Run02.fastq.gz" \
  --barcodes       "$(pwd)/Run02/Run02_barcodes.fasta" \
  --primer_forward "GTACACACCGCCCGTCG" \
  --primer_reverse "CCTSCSCTTANTDATATGC" \
  --outdir         "Step1_Results/Run02"
```

Sequencing run #3:  
``` bash
nextflow run vmikk/NextITS -r main \
  -profile singularity \
  -resume \
  --input          "$(pwd)/Run03/Run03.fastq.gz" \
  --barcodes       "$(pwd)/Run03/Run03_barcodes.fasta" \
  --primer_forward "GTACACACCGCCCGTCG" \
  --primer_reverse "CCTSCSCTTANTDATATGC" \
  --outdir         "Step1_Results/Run03"
```
