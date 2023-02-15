[![Docker Image CI](https://github.com/mattgalbraith/salmon-docker-singularity/actions/workflows/docker-image.yml/badge.svg)](https://github.com/mattgalbraith/salmon-docker-singularity/actions/workflows/docker-image.yml)
# salmon-docker-singularity
## Build Docker container for salmon software and (optionally) convert to Apptainer/Singularity  
Salmon is a tool for quantifying the expression of transcripts using RNA-seq data  
Dockerfile modified from https://github.com/COMBINE-lab/salmon/blob/master/docker/Dockerfile

## Build docker container:  

### 1. For salmon installation instructions:  
https://combine-lab.github.io/salmon/getting_started/   

### 2. Build the Docker Image

#### To build image from the command line:  
``` bash
# Assumes current working directory is the top-level salmon-docker-singularity directory
docker build -t salmon:1.9.0 .
```
* Can do this on [Google shell](https://shell.cloud.google.com)

#### To test this tool from the command line:
```bash
docker run --rm -it salmon:1.9.0 salmon -h
```

## Optional: Conversion of Docker image to Singularity  

### 3. Build a Docker image to run Singularity  
(skip if this image is already on your system)  
https://github.com/mattgalbraith/singularity-docker


### 4. Save Docker image as tar and convert to sif (using singularity run from Docker container)  
``` bash
docker images
docker save <Image_ID> -o salmon-docker1.9.0.tar && gzip salmon-docker1.9.0.tar # = IMAGE_ID of salmon image
docker run -v "$PWD:/data" --rm -it singularity bash -c "singularity build /data/salmon1.9.0.sif docker-archive:///data/salmon-docker1.9.0.tar.gz"
```
NB: On Apple M1/M2 machines ensure Singularity image is built with x86_64 architecture or sif may get built with arm64  

Next, transfer the salmon.sif file to the system on which you want to run Salmon from the Singularity container

### 5. Test singularity container on (HPC) system with Singularity/Apptainer available
```bash
# set up path to the Salmon Singularity container
SALMON_SIF=path/to/salmon.sif

# Test that Salmon can run from Singularity container
singularity run $SALMON_SIF salmon -h # depending on system/version, singularity is now called apptainer
```

