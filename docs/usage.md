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

Depending on the DNA library design, your amplicon sequences could be tagged with barcodes in different ways.
NextITS supports multiple multiplexing strategies that can be specified with the `--lima_barcodetype` parameter:

- `dual_symmetric` - identical barcodes at both ends (default)
- `dual_asymmetric` - different barcodes at 5' and 3' ends
- `dual` - a mixture of symmetric and asymmetric barcodes
- `single` - barcodes only at one end

For dual-barcoding schemes, provide barcode pairs in the FASTA file using the following format:

```
>RunID__Sample_01
ACAACACTCCGA...TGCTAGCTAGCT
>RunID__Sample2
ACAAGTGCTGCT...GCATGCATGCAT
```

!!! info "Unique barcodes"
    Make sure that the provided sample names and barcode sequences (or barcode combinations) are unique!

