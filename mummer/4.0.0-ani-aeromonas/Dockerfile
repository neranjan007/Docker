FROM ubuntu:focal AS app

# ARG sets environment variables during the build stage; they do not persist after the image is built
ARG MUMMER_VER="4.0.0"
ARG MASH_VER="2.3"

LABEL base.image="ubuntu:focal"
LABEL dockerfile.version="1"
LABEL software="mummer"
LABEL software.version=${MUMMER_VER}
LABEL description="MUMmer is a versatile alignment tool for DNA and protein sequences."
LABEL website="https://github.com/mummer4/mummer"
LABEL license="https://github.com/mummer4/mummer/blob/master/LICENSE.md"
LABEL maintainer="Neranjan Perera"
LABEL maintainer.email="neranjan007@gmail.com"


# to prevent tzdata from asking for a region during apt updates
ARG DEBIAN_FRONTEND=noninteractive

# Install dependencies via apt; clean up apt garbage
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    git \
    libncurses5-dev \
    libbz2-dev \
    liblzma-dev \
    libcurl4-gnutls-dev \
    zlib1g-dev \
    libssl-dev \
    gcc \
    make \
    perl \
    bzip2 \
    gnuplot \
    ca-certificates \
    gawk \
    curl \
    sed \
    build-essential \
    unzip && \
    apt-get autoclean && rm -rf /var/lib/apt/lists/*

# install mummer; make /data for use as working directory
RUN wget https://github.com/mummer4/mummer/releases/download/v${MUMMER_VER}rc1/mummer-${MUMMER_VER}rc1.tar.gz && \
    tar -xvf mummer-${MUMMER_VER}rc1.tar.gz && \
    rm mummer-${MUMMER_VER}rc1.tar.gz && \
    cd mummer-${MUMMER_VER}rc1 && \
    ./configure && \
    make && \
    make install && \
    ldconfig && \
    cd .. && \
    mkdir /data

# install ncbi datasets tool (pre-compiled binary); place in $PATH
RUN wget https://ftp.ncbi.nlm.nih.gov/pub/datasets/command-line/LATEST/linux-amd64/datasets && \
    chmod +x datasets && \
    mv -v datasets /usr/local/bin

# copy in list of NCBI accessions and species list
COPY aeromonas-NCBI-assembly-accessions.txt /DB/aeromonas-NCBI-assembly-accessions.txt
COPY aeromonas-NCBI-assembly-accessions-and-species.txt /DB/aeromonas-NCBI-assembly-accessions-and-species.txt

# download RGD genomes using NCBI datasets tools; cleanup unneccessary files; 
# move and re-name assemblies to include Species in the filename
# make fasta files readable to all users; create File Of FileNames for all 43 assemblies (to be used with fastANI)
RUN for ID in $(cat /DB/aeromonas-NCBI-assembly-accessions.txt); do \
        SPECIES=$(grep "${ID}" /DB/aeromonas-NCBI-assembly-accessions-and-species.txt | cut -f 1) && \
        echo "downloading $ID, species "${SPECIES}", from NCBI..."; \
        datasets download genome accession --filename ${ID}.zip ${ID}; \
        unzip -q ${ID}.zip; \
        rm ${ID}.zip; \
        mv -v ncbi_dataset/data/${ID}/${ID}*.fna /DB/${ID}.${SPECIES}.fasta; \
        rm -rf ncbi_dataset/; \
        rm README.md; \
    done && \
    ls /DB/*.fasta >/DB/FOFN-gas.txt && \
    chmod 664 /DB/*

# downloading mash binary
RUN wget https://github.com/marbl/Mash/releases/download/v${MASH_VER}/mash-Linux64-v${MASH_VER}.tar && \
    tar -xvf mash-Linux64-v${MASH_VER}.tar --no-same-owner && \
    rm -rf mash-Linux64-v${MASH_VER}.tar

# COPY mash-Linux64-v2.3/mash /mash-Linux64-v2.3/mash
# COPY mash-Linux64-v2.3/LICENSE.txt /mash-Linux64-v2.3/LICENSE.txt

# download Lee's ani-m script
 RUN wget https://github.com/lskatz/ani-m/archive/refs/tags/v0.1.tar.gz && \
    tar xzf v0.1.tar.gz && \
    rm -v v0.1.tar.gz

# LC_ALL for singularity compatibility; set PATH
ENV LC_ALL=C \
    PATH="${PATH}:/ani-m-0.1:/mash-Linux64-v${MASH_VER}"

# set working directory
WORKDIR /data

