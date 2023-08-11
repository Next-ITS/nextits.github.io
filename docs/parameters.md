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

NextITS boasts a vast array of configurable parameters. 
For ease of navigation and understanding, these parameters have been grouped into several categories, 
detailed in the sections that follow.  

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


### Quality filtering

| Parameter                | Description                                                                          | Default Value |
| ------------------------ | ------------------------------------------------------------------------------------ | ------------- |
| `--qc_maxee`             | Maximum number of expected errors                                                    | false         |
| `--qc_maxeerate`         | Maximum number of expected errors per base                                           | 0.01          |
| `--qc_maxhomopolymerlen` | Threshold for a homopolymer region length in a sequence                              | 25            |
| `--qc_maxn`              | Discard sequences with more than the specified number of ambiguous nucleotides (N's) | 4             |


### Removal of multiprimer-artifacts and reorienting of reads

| Parameter             | Description                                | Default Value       |
| --------------------- | ------------------------------------------ | ------------------- |
| `--primer_forward`    | Sequence of the forward primer ^1^         | TACACACCGCCCGTCG    |
| `--primer_reverse`    | Sequence of the reverse primer ^1^         | CCTSCSCTTANTDATATGC |
| `--primer_mismatches` | Allowed number of mismatches for primers   | 2                   |
| `--primer_foverlap`   | Minimum overlap for the forward primer ^2^ | F primer length - 2 |
| `--primer_roverlap`   | Minimum overlap for the reverse primer ^2^ | R primer length - 2 |

**Default Primers:**  
By default, NextITS is configured for the data obtained with universal eukaryote primers:  
- **Forward Primer:** `ITS9MUNngs` with sequence `TACACACCGCCCGTCG`  
- **Reverse Primer:** `ITS4ngsUni` with sequence `CCTSCSCTTANTDATATGC`  
Refer to [Tedersoo & Lindahl, 2016 (DOI:10.1111/1758-2229.12438)](https://ami-journals.onlinelibrary.wiley.com/doi/10.1111/1758-2229.12438) for more details on these primers.  

**Alternative Forward Primer:**  
For specific applications, we also recommend the `ITS1catta` forward primer (sequence: `ACCWGCGGARGGATCATTA`). 
It's designed to target and exclude plant sequences while avoiding interference from the SSU 3′-end intron.  
For additional information on this primer, consult [Tedersoo & Anslan, 2019 (DOI:10.1111/1758-2229.12776)](https://ami-journals.onlinelibrary.wiley.com/doi/10.1111/1758-2229.12776).  

### ITS extraction

| Parameter        | Description                                                                     | Default Value |
| ---------------- | ------------------------------------------------------------------------------- | ------------- |
| `--its_region`   | ITS part selector ^2^                                                           | "full"        |
| `--ITSx_tax`     | Taxonomy profile for ITSx ^1^                                                   | "all"         |
| `--ITSx_evalue`  | E-value cutoff threshold for ITSx                                               | 1e-1          |
| `--ITSx_partial` | Keep partial ITS sequences (specify a minimum length cutoff), default = off (0) | 0             |

When performing ITS metabarcoding, it's essential to trim the flanking 18S and 28S rRNA genes. 
This is crucial because:  
- These conserved regions don't offer species-level differentiation.  
- Random errors in these areas can disrupt sequence clustering.  
- Chimeric breakpoints, which are common in these regions, are hard to detect in short fragments ranging from 10 to 70 bases.  
(For details, see [Lindahl et al. 2013, DOI:10.1111/nph.12243)](https://nph.onlinelibrary.wiley.com/doi/10.1111/nph.12243) and
 [Tedersoo et al. 2022 (DOI:10.1111/mec.16460)](https://onlinelibrary.wiley.com/doi/full/10.1111/mec.16460)).  

**Utilizing ITSx in NextITS:**  
To address this, NextITS employs the ITSx program ([Bengtsson-Palme et al., 2013 (DOI:10.1111/2041-210X.12073)](https://besjournals.onlinelibrary.wiley.com/doi/10.1111/2041-210X.12073)).  
By using the `--its_region` parameter, users can select the region for subsequent analyses.  
Supported options include:  
- **full**: Represents the full-length ITS  
- **ITS1_5.8S_ITS2**: A near-full-length ITS that assembles the sequence from the `ITS1`, `5.8S`, and `ITS2` segments extracted by ITSx.  
  This option is particularly handy when ITSx struggles to detect the end of the SSU. 
  (for instance, when using the `ITS1catta` primer, which is located at the extreme end of the SSU and is not detected by ITSx).  
- **ITS1**: Represents the ITS1 region  
- **ITS2**: Represents the ITS2 region  
- **SSU**: Focuses on the small subunit of rRNA (18S)  
- **LSU**: Focuses on the large subunit of rRNA (28S)  
- **none**: This option only trims primers without extracting the ITS  

### Chimera identification

| Parameter                       | Description                                                                      | Default Value |
|---------------------------------|----------------------------------------------------------------------------------|---------------|
| `--chimera_db`                  | Database for reference-based chimera removal (UDB)                               | -             |
| `--chimera_rescueoccurrence`    | Min occurrence of chimeric sequences required to rescue them                     | 2             |
| `--chimeranov_abskew`           | `abskew` parameter for de novo chimera identification                            | 2.0           |
| `--chimeranov_dn`               | `dn` parameter for de novo chimera identification                                | 1.4           |
| `--chimeranov_mindiffs`         | `mindiffs` parameter for de novo chimera identification                          | 3             |
| `--chimeranov_mindiv`           | `mindiv` parameter for de novo chimera identification                            | 0.8           |
| `--chimeranov_minh`             | `minh` parameter for de novo chimera identification                              | 0.28          |
| `--chimeranov_xn`               | `xn` parameter for de novo chimera identification                                | 8.0           |

NextITS employs a two-pronged strategy to detect chimeras:  

1. ***De novo* Detection**:  
  This algorithm identifies chimeras without relying on reference data. 
  To mitigate the risk of false-positive detections, sequences flagged as chimeras by the *de novo* method are not discarded instantly. 
  Instead, they're assigned a chimeric score. Filtering based on this score can be conducted in subsequent analysis stages (refer to Step-2 for more details).  

2. **Reference-based Detection**:  
  This method leverages a reference database in the UDB format.  
  An example of such a database is accessible at: [https://owncloud.ut.ee/owncloud/s/iaQ3i862pjwYgdy](https://owncloud.ut.ee/owncloud/s/iaQ3i862pjwYgdy).  

Furthermore, NextITS offers an option to "rescue" sequences mistakenly labeled as chimeras.  
The pipeline checks if these sequences appear in other samples where they are not identified as chimeras. 
If a sequence is consistently observed, it's more likely to be a genuine biological sequence. 
You can control this functionality using the `--chimera_rescueoccurrence` parameter.  


### Homopolymer correction

| Parameter         | Description                                               | Default Value |
| ----------------- | --------------------------------------------------------- | ------------- |
| `--hp`            | Enable or disable homopolymer correction                  | `true`        |
| `--hp_similarity` | Allowed sequence similarity for homopolymer correction    | 0.999         |
| `--hp_iddef`      | Sequence similarity definition for homopolymer correction | 2             |


### Tag-jump Removal Parameters

| Parameter     | Description                                              | Default Value |
| ------------- | -------------------------------------------------------- | ------------- |
| `--tj_f`      | UNCROSS parameter `f` for tag-jump filtering             | 0.01          |
| `--tj_p`      | Parameter `p` for tag-jump filtering                     | 1             |
| `--otu_id`    | Sequence similarity for OTU clustering                   | 0.98          |
| `--otu_iddef` | Sequence similarity definition for tag-jump removal step | 2             |

### Miscellaneous parameters

| Parameter            | Description                    | Default Value |
| -------------------- | ------------------------------ | ------------- |
| `--gzip_compression` | Compression level for GZIP ^1^ | "-7"          |

^1^:
    `-1` = fastest (worst compression), `-9` = slowest (best)


## Step-2

### Required parameters

| Parameter     | Description                                                     |
| ------------- | --------------------------------------------------------------- |
| `--data_path` | Input directory containing the results of Step-1                |
| `--outdir`    | Path to the directory where the analysis results will be saved. |

### Denoising parameters (optional)

| Parameter          | Description                             | Default Value |
| ------------------ | --------------------------------------- | ------------- |
| `--unoise`         | Perform denoising with UNOISE algorithm | `false`       |
| `--unoise_alpha`   | `Alpha` parameter of UNOISE             | 2.0           |
| `--unoise_minsize` | Minimum sequence abundance              | 8             |


### Sequence clustering

| Parameter              | Description                                   | Default Value |
|------------------------|-----------------------------------------------|---------------|
| `--clustering_method`  | Sequence clustering method                    | "vsearch"     |

Supported Methods:  
- **vsearch**: This employs greedy clustering using a fixed sequence similarity threshold;  
- **swarm**: Unlike "vsearch", this uses a dynamic sequence similarity threshold for clustering;  
- **unoise**: This focuses solely on denoising to create zero-radius OTUs (zOTUs);  
- **shmatching** (currently under development): This method clusters sequences based on species hypotheses (SH) as detailed in Kõljalg et al. 2013 (DOI:10.1111/mec.12481).  


#### VSEARCH clustering

| Parameter     | Description                                                         | Default Value |
| ------------- | ------------------------------------------------------------------- | ------------- |
| `--otu_id`    | Sequence similarity threshold                                       | 0.98          |
| `--otu_iddef` | Sequence similarity definition (applied to UNOISE as well)          | 2             |
| `--otu_qmask` | Method to mask low-complexity sequences (applied to UNOISE as well) | "dust"        |

#### SWARM clustering

| Parameter            | Description                                                   | Default Value |
| -------------------- | ------------------------------------------------------------- | ------------- |
| `--swarm_d`          | SWARM clustering resolution (`d`)                             | 1             |
| `--swarm_fastidious` | Link nearby low-abundance swarms (`fastidious` option)        | `true`        |
| `--swarm_d1boundary` | Minimal mass of large OTUs (only for `fastidious` with `d`=1) | 3             |
