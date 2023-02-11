################## BASE IMAGE ######################
FROM ubuntu:20.04 as base

################## METADATA ######################
LABEL base_image="ubuntu:20.04"
LABEL version="2"
LABEL software="Salmon"
LABEL software.version="1.9.0"
LABEL about.summary="Salmon is a tool for quantifying the expression of transcripts using RNA-seq data"
LABEL about.home="https://combine-lab.github.io/salmon/"
LABEL about.documentation="https://salmon.readthedocs.io/en/latest/"
LABEL about.license_file="https://github.com/COMBINE-lab/salmon/blob/master/LICENSE"
LABEL about.license="GNU General Public License v3.0"

################## MAINTAINER ######################
MAINTAINER Matthew Galbraith <matthew.galbraith@cuanschutz.edu>
# This dockerfile is based on the one available at https://github.com/COMBINE-lab/salmon/blob/master/docker/Dockerfile

################## INSTALLATION ######################
ENV PACKAGES git gcc make g++ libboost-all-dev liblzma-dev libbz2-dev \
    ca-certificates zlib1g-dev libcurl4-openssl-dev curl unzip autoconf apt-transport-https ca-certificates gnupg software-properties-common wget
ENV SALMON_VERSION 1.9.0

# salmon binary will be installed in /home/salmon/bin/salmon

### don't modify things below here for version updates etc.

WORKDIR /home

RUN apt-get update && \
    apt remove -y libcurl4 && \
    apt-get install -y --no-install-recommends ${PACKAGES} && \
    apt-get clean

RUN wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | apt-key add -

RUN apt-add-repository 'deb https://apt.kitware.com/ubuntu/ bionic main'

RUN apt-get update

RUN apt-key --keyring /etc/apt/trusted.gpg del C1F34CDD40CD72DA

RUN apt-get install kitware-archive-keyring

RUN apt-get install -y cmake

RUN curl -k -L https://github.com/COMBINE-lab/salmon/archive/v${SALMON_VERSION}.tar.gz -o salmon-v${SALMON_VERSION}.tar.gz && \
    tar xzf salmon-v${SALMON_VERSION}.tar.gz && \
    cd salmon-${SALMON_VERSION} && \
    mkdir build && \
    cd build && \
    cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local/salmon && make && make install

# For dev version
#RUN git clone https://github.com/COMBINE-lab/salmon.git && \
#    cd salmon && \
#    git checkout develop && \
#    mkdir build && \
#    cd build && \
#    cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local && make && make install

FROM ubuntu:20.04
RUN apt-get update \
    && apt-get install -y --no-install-recommends libhwloc5 \
    && rm -rf /var/lib/apt/lists/*
COPY --from=base /usr/local/salmon/ /usr/local/
ENV PATH /home/salmon-${SALMON_VERSION}/bin:${PATH}
ENV LD_LIBRARY_PATH "/usr/local/lib:${LD_LIBRARY_PATH}"

RUN echo "export PATH=$PATH" > /etc/environment
RUN echo "export LD_LIBRARY_PATH=$LD_LIBRARY_PATH" > /etc/environment