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

## Manual installation

### [`Nextflow`](https://www.nextflow.io/docs/latest/getstarted.html#installation) 

Nextflow (`>=22.10.0`) requires Java 11 (or later, up to 18) to be installed.

``` bash
sudo apt-get update
sudo apt-get install default-jdk
```

Install Nextflow:

```bash
wget -qO- https://get.nextflow.io | bash
chmod +x ./nextflow
mkdir -p ~/bin & mv ./nextflow ~/bin/
```


### [`Singularity`](https://sylabs.io/docs/)

To install Singularity on Debian-based systems (including Ubuntu), run:

``` bash
## Download dependencies
sudo apt update && sudo apt install -y \
    build-essential libssl-dev uuid-dev libgpgme11-dev libarchive-dev \
    libseccomp-dev wget pkg-config git cryptsetup dh-autoreconf squashfs-tools

## Download GO
export VERSION=1.18.1 OS=linux ARCH=amd64 && \
  wget https://dl.google.com/go/go$VERSION.$OS-$ARCH.tar.gz && \
  sudo tar -C /usr/local -xzvf go$VERSION.$OS-$ARCH.tar.gz && \
  rm go$VERSION.$OS-$ARCH.tar.gz

echo 'export GOPATH=${HOME}/go' >> ~/.bashrc && \
echo 'export PATH=/usr/local/go/bin:$PATH' >> ~/.bashrc && \
source ~/.bashrc

## Check Go installation
go env

## If conda points to wrong locations for Go, reassign environmental variables
export GOTOOLDIR="/usr/local/go/pkg/tool/linux_amd64"
export GOROOT="/usr/local/go"

## Download singularity
export VERSION=3.9.8 && \
  wget https://github.com/sylabs/singularity/releases/download/v${VERSION}/singularity-ce-${VERSION}.tar.gz && \
  tar -xzf singularity-ce-${VERSION}.tar.gz && \
  cd singularity-ce-${VERSION}

## Compile and install Singularity
./mconfig && \
  make -C builddir && \
  sudo make -C builddir install

```

!!! info "Singularity on HPC clusters"
    If you are working on an HPC cluster, your IT team probably installed Singularity as a Linux Environment Module.  
    You may check Singularity availability using  
    ```module avail singularity```  
    If it is present on your system, you will need to load the module prior to running NextITS.  
    E.g., ```module load singularityce/3.9.1```  

