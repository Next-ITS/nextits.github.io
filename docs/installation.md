---
title: Installation
description: >-
    Learn how to install the pipeline and its dependencies and how get started using NextITS for your analyses.
---

# Installation

NextITS was primarily developed for Linux. To run it from a command line on Windows, please use Windows Subsystem for Linux ([WSL](https://learn.microsoft.com/en-us/windows/wsl/install)).

All pipeline dependencies were encapsulated in Singularity and Docker containers, 
which will be automatically downloaded with the first run of NextITS. 
Only the main dependencies must be installed manually. They include:  

- [Java](https://www.oracle.com/java/technologies/downloads/)  
- [Nextflow](https://www.nextflow.io/)  
- [Singularity](https://docs.sylabs.io/guides/latest/user-guide/) or [Docker](https://www.docker.com/) container engine


## Nextflow

[Nextflow](https://www.nextflow.io/) is the workflow manager responsible for 
coordinating and executing all command line tools seamlessly. 

### Manual installation

[`Nextflow`](https://www.nextflow.io/docs/latest/getstarted.html#installation) (`>=23.04.0`) 
requires Java 11 (or later, up to 20) to be installed.

To install Java, you may use your system's package manager.  
If you're using Debian/Ubuntu, execute the following command:  
``` bash
sudo apt-get update
sudo apt-get install default-jdk
```

Alternatively, you may install Java using [SDKMAN!](https://sdkman.io/):  
``` bash
curl -s "https://get.sdkman.io" | bash
sdk install java 17.0.6-amzn
```

Then, install Nextflow:

```bash
wget -qO- https://get.nextflow.io | bash
chmod +x ./nextflow
mkdir -p ~/bin & mv ./nextflow ~/bin/
```

### Installation using `conda`

It is possible to install the basic dependencies using [`conda`](https://docs.conda.io/en/latest/), 
a package and environment management system. Here, we will use `mamba`, 
which is a reimplementation of the `conda` package manager in C++ 
(it has a much faster dependency solver).  

``` bash
mamba install -c bioconda nextflow
```

If you do not have `conda` and `mamba`, they may be installed running the following commands:  

``` bash
## Install `miniconda`
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /tmp/miniconda.sh
bash /tmp/miniconda.sh -b -p $HOME/miniconda
~/miniconda/bin/conda init bash
source ~/.bashrc
rm /tmp/miniconda.sh

## Install `mamba`
conda install -c conda-forge mamba
```

!!! info "Conda"
    For more details, see [https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html)


## Container engines

NextITS integrates numerous bioinformatic tools, and managing their installations and updates can be quite intricate for users. 
To simplify this, all these tools are packaged within containers. 
This not only makes installations hassle-free but also ensures consistent software versions, 
guaranteeing reproducible results across different systems.  

NextITS supports two container systems: 
[Singularity](https://sylabs.io/docs/) and 
[Docker](https://www.docker.com/).  

Singularity is often the preferred choice for High-Performance Computing (HPC) environments due to its compatibility and security features. 
On the other hand, Docker might be a tad easier to set up on local systems, making it a popular choice for personal use.  

### Singularity

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

### Docker

To install Docker on Ubuntu, run the following commands.  
For more details, see the [Docker installation instructions](https://docs.docker.com/engine/install/ubuntu/).  

``` bash
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

## Add Docker’s official GPG key:
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

## Set up the repository
# Ubuntu
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker   
```

## Databases

For ITS amplicons, you may use the following database for reference-based chimera removal:  
[https://owncloud.ut.ee/owncloud/s/iaQ3i862pjwYgdy](https://owncloud.ut.ee/owncloud/s/iaQ3i862pjwYgdy).  
This database originates from the most recent version of [UNITE](https://unite.ut.ee/index.php), version 9. 

## Test

### Download the pipeline and test it on a minimal dataset with a single command

``` bash
nextflow run vmikk/nextits -r main -profile test
```


## Containers

### Singularity
#### Download container from the Singularity library

``` bash
singularity pull --arch amd64 library://vmiks/nextits/nextits:0-0-5
```

#### Build custom Singularity image

``` bash
git clone https://github.com/vmikk/NextITS
sudo singularity build NextITS.sif ./NextITS/containerfiles/main_container.def
```

### Docker
#### Download container from Docker Hub

``` bash
docker pull vmikk/nextits
```

#### Build custom image

``` bash
git clone https://github.com/vmikk/NextITS
docker build --tag nextits --file NextITS/containerfiles/NextITS.dockerfile .
```

### Other container engines

!!! example "Other container engines"
     In the future, we plan to add support for the other container engines 
     (e.g., `Podman`, `Shifter`, or `Charliecloud`) in NextITS.  


