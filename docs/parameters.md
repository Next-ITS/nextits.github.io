---
title: Parameters
description: >-
    Pipeline parameters and configuration.
---

# Parameters

The NextITS pipeline is made up of multiple connected processes. 
Each of these processes is amenable to configuration, 
empowering users to tailor not just the process parameters 
but also the resources allocated for each one (e.g., the number of CPUs). 
When setting the pipeline parameter values, users have two choices: 
they can specify them directly from the command line using double dash arguments (for example, `--its_region "full"`) 
or opt for a simple parameters file. Utilizing a parameters file is especially advantageous 
when dealing with numerous settings, as it offers a cleaner command line interface. 
In addition to this, individual processes within the pipeline can be fine-tuned using a dedicated config file.  
## Step-1

### Required parameters

| Parameter    | Description                                                                                                            |
| ------------ | ---------------------------------------------------------------------------------------------------------------------- |
| `--input`    | Path to the input file containing single-end sequences (in FASTQ format) or the directory with pre-demultiplexed files |
| `--barcodes` | Path to the file with barcodes (in FASTA format) used for demultiplexing the input data                                |
| `--outdir`   | Path to the directory where the analysis results will be saved.                                                        |


### Demultiplexing

| Parameter            | Description                                                                                                       | Default Value |
| -------------------- | ----------------------------------------------------------------------------------------------------------------- | ------------- |
| `--demultiplexed`    | Whether input is multiplexed (`true`, single FASTQ file) or pre-demultiplexed (`false`, multiple FASTQ files) ^1^ | `true`        |
| `--lima_minscore`    | Barcode score for demultiplexing                                                                                  | 93            |
| `--lima_dualbarcode` | Dual-barcoding scheme with identical barcodes at both ends ^2^                                                    | `true`        |
| `--lima_W`           | Window size for barcode lookup                                                                                    | 70            |
| `--lima_minlen`      | Minimum sequence length after clipping barcodes                                                                   | 40            |

