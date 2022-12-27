---
title: Installation
description: >-
    Learn how to install the pipeline and its dependencies and how get started using NextITS for your analyses.
---

# Installation

NextITS was primarily developed for Linux. To run it from a command line on Windows, please use Windows Subsystem for Linux ([WSL](https://learn.microsoft.com/en-us/windows/wsl/install)).

All pipeline dependencies were encapsulated in Singularity containers, which will be automatically downloaded with the first run of NextITS. Only the main dependencies must be installed manually. They include:  

- [Java](https://www.oracle.com/java/technologies/downloads/)  
- [Nextflow](https://www.nextflow.io/)  
- [Singularity](https://docs.sylabs.io/guides/latest/user-guide/) container engine

The following installation instruction is for Ubuntu.

## Install [`Nextflow`](https://www.nextflow.io/docs/latest/getstarted.html#installation) 

Nextflow (`>=22.10.0`) requires Java 11 (or later, up to 18) to be installed.

```{bash}
sudo apt-get update
sudo apt-get install default-jdk
```

Install Nextflow:

```{bash}
wget -qO- https://get.nextflow.io | bash
chmod +x ./nextflow
mkdir -p ~/bin & mv ./nextflow ~/bin/
```

!!! note "Conda"
    Alternatively, one can install Nextflow via [conda](https://docs.conda.io/en/latest/),
    a package and environment management system.  
    See [here](https://anaconda.org/bioconda/nextflow) for additional instructions.

