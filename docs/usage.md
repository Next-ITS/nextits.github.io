---
title: Basic Usage
description: >-
    NextITS pipeline usage examples.
---

# NextITS pipeline usage examples

## Quick Start

```
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
