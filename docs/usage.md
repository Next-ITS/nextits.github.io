---
title: Usage instructions
description: >-
    NextITS pipeline usage examples.
---

# NextITS pipeline usage examples

## Quick Start

``` bash
nextflow run vmikk/NextITS -r main \
  -profile singularity \
  -resume \
  --input          "pacbio_ccs.fastq.gz" \
  --barcodes       "sample_barcodes.fasta" \
  --primer_forward "GTACACACCGCCCGTCG" \
  --primer_reverse "CCTSCSCTTANTDATATGC" \
  --its_region     "full" \
  --blast_taxdb    "false" \
  --outdir         "Results"
```

## Input data

### Sequences

For PacBio data, NextITS requires a single FASTQ file (`--input` argument) with multiplexed samples.  

### Barcodes

For demultiplexing, sample barcodes (a.k.a. tags or indices) should be provided in a FASTA file 
and have the following format:  
```
>RunID__Sample_01
ACAACACTCCGA
>RunID__Sample2
ACAAGTGCTGCT
>RunID__PosC
ACACAGTCCTGA
```

In NextITS, we use the `SequencingRunID__SampleID` naming convention 
(please note the **double underscore** separating `RunID` and `SampleID` parts). 
This naming scheme allows to easily trace back sequences, especially if the same sample 
was sequenced several times and is present in multiple sequencing runs. 
In the later steps, extracting the `SampleID` part and summarizing read counts for such samples is easy.  

!!! info "Sample naming"
    Please avoid non-ASCII symbols in `RunID` and `SampleID`,  
    and do not use the period symbol (`.`), as it represents the wildcard character in regular expressions.  
    Also, it is preferable not to start the sample name with a number.  

Demultiplexing of PacBio data is performed using [`lima` software](https://lima.how/faq/). 
To identify best-matching barcodes, *lima* computes the alignment score of each barcode. 
These scores depend on the barcode length. 
In NextITS, the default threshold for the minimum accepted score is provided with the expectation that barcodes have a length of 12 bp. 
If your barcodes are shorter or you use barcodes of different lengths, you may need to adjust the `--lima_minscore` parameter.  

Depending on the DNA library design, your amplicon sequences could be flanked by barcodes 
either on both sides (dual barcodes) or just on one side (5' or 3').
By default, NextITS expects dual-barcoding scheme (with identical barcodes at both ends). 
If you use barcodes at a single end, please add the `--lima_dualbarcode false` parameter to your command.  

!!! info "Unique barcodes"
    Make sure that the provided sample names and barcode sequences are unique!

!!! example "Combinatorial sequence barcodes"
    It is possible to tag multiplexed samples with a pair of **different barcodes** attached to the sides of the amplicon. 
    Currently, there is no support for this asymmetric design in NextITS, but it will be added in a future release.


## Singularity containers

### Download container from the Singularity library

``` bash
singularity pull --arch amd64 library://vmiks/nextits/nextits:0-0-2
```

### Build custom Singularity image

``` bash
git clone https://github.com/vmikk/NextITS
sudo singularity build NextITS.sif ./NextITS/containerfiles/main_container.def
```
