[![Docker Image CI](https://github.com/mattgalbraith/salmon-docker-singularity/actions/workflows/docker-image.yml/badge.svg)](https://github.com/mattgalbraith/salmon-docker-singularity/actions/workflows/docker-image.yml)
# salmon-docker-singularity
## Build Docker container for salmon software and (optionally) convert to Apptainer/Singularity  
Salmon is a tool for quantifying the expression of transcripts using RNA-seq data  
Dockerfile modified from https://github.com/COMBINE-lab/salmon/blob/master/docker/Dockerfile

## Build docker container:  

### 1. For salmon installation instructions:  
https://combine-lab.github.io/salmon/getting_started/   

### 2. Build the Docker Image

#### To build your image from the command line:
* Can do this on [Google shell](https://shell.cloud.google.com) - docker is installed and available

```bash
WORKING_DIR=`pwd` # capture current working directory (should be the top-level salmon-docker-singularity directory)
cd salmon-docker
docker build -t salmon .
```

#### To test this tool from the command line:
Mount and use your current directory and call the tool now encapsulated within the container
```bash
docker run --rm -it -v "$PWD":"$PWD" -w "$PWD" salmon salmon -h
```

## Optional: Conversion of Docker image to Singularity

### 4. Build a Docker image to run Singularity

```bash
cd $WORKING_DIR/singularity-docker
docker build -t singularity .
```

#### Test singularity container
```bash
docker run --rm -it -v $PWD:$PWD -w $PWD singularity singularity
```

### 5. Save Docker image as tar and convert to sif (using singularity run from Docker container)
```bash
cd $WORKING_DIR
docker save <Image_ID> -o salmon-docker.tar # = IMAGE_ID of salmon image
docker run -v "$PWD:/out" --rm -it singularity bash -c "singularity build /out/salmon.sif docker-archive:///out/salmon-docker.tar"
```
NB: may build with arm64 architecture if run on M1/M2 Macbook  

Next, transfer the salmon.sif file to the system on which you want to run Salmon from the Singularity container

### 6. Test singularity container on (HPC) system with Singularity/Apptainer available
```bash
# set up path to the Salmon Singularity container
SALMON_SIF=path/to/salmon.sif

# Test that Salmon can run from Singularity container
singularity run $SALMON_SIF salmon -h # depending on system/version, singularity is now called apptainer
```

