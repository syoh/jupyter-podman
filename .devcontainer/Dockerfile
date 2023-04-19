# R + Python + Julia kernels
FROM docker.io/jupyter/datascience-notebook:x86_64-2023-03-06
# FROM jupyter/scipy-notebook:x86_64-2023-03-06
# FROM jupyter/r-notebook:x86_64-2023-03-06

USER root

### System dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        lmodern file curl && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/* /tmp/library-scripts

### Quarto
# versions: https://quarto.org/docs/download/_download.json
# neat setup: https://github.com/jeremiahpslewis/reproducibility-with-quarto
RUN curl --silent -L --fail \
        https://github.com/quarto-dev/quarto-cli/releases/download/v1.2.269/quarto-1.2.269-linux-amd64.deb > /tmp/quarto.deb && \
    apt-get update && \
    apt-get install -y --no-install-recommends /tmp/quarto.deb && \
    rm -rf /tmp/quarto.deb /var/lib/apt/lists/* /tmp/library-scripts

RUN apt update -qq && \
    apt install software-properties-common -y && \
    add-apt-repository ppa:nrbrtx/libssl1 && \
    apt update -qq && \
    apt upgrade -y && \
    apt install -y \
        libssl-dev
        # software-properties-common \
        # lsof \
        # less \
        # libapparmor1 \
        # libtiff5-dev \
        # libfftw3-dev \
        # libcairo2-dev \
        # libx11-dev \
        # x11-utils \
        # psmisc \
        # libclang-dev \
        # gfortran \
        # libv8-dev \
        # libssh2-1-dev \
        # git \
        # git-lfs \
        # curl \
        # libuser \
        # libuser1-dev \
        # libpq-dev \
        # rrdtool \  
        # build-essential \
        # libxml2-dev \
        # libcurl4-openssl-dev \
        # build-essential \
        # cmake \
        # libnlopt-dev \
        # libboost-all-dev \
        # wget \
        # lmodern
        
ENV R_STUDIO_VERSION 2022.12.0-353

## Install rstudio from source package
RUN wget -q https://download1.rstudio.org/electron/jammy/amd64/rstudio-${R_STUDIO_VERSION}-amd64.deb && \
    wget -q https://download2.rstudio.org/server/jammy/amd64/rstudio-server-${R_STUDIO_VERSION}-amd64.deb && \
    apt install ./rstudio*.deb -yq && rm -f ./rstudio*.deb && \
    apt-get clean && \
    chmod 777 /var/run/rstudio-server && \
    chmod +t /var/run/rstudio-server

# RUN R -e "dotR <- file.path(Sys.getenv('HOME'), '.R'); if(!file.exists(dotR)){ dir.create(dotR) }; Makevars <- file.path(dotR, 'Makevars'); if (!file.exists(Makevars)){  file.create(Makevars) }; cat('\nCXX14FLAGS=-O3 -fPIC -Wno-unused-variable -Wno-unused-function', 'CXX14 = g++ -std=c++1y -fPIC', 'CXX = g++', 'CXX11 = g++', file = Makevars, sep = '\n', append = TRUE)"

RUN pip install nbgitpuller && \
    jupyter serverextension enable --py nbgitpuller --sys-prefix 

# RUN mamba install -y -c conda-forge libwebp
    
RUN mamba install -y -c conda-forge --freeze-installed jupyter-server-proxy jupyter-rsession-proxy && \
    mamba clean --all

RUN /usr/local/bin/fix-permissions "${CONDA_DIR}" || true


USER ${NB_USER}

### Prints Jupyter server token when terminal is opened
RUN echo "echo \"Jupyter server token: \$(jupyter server list 2>&1 | grep -oP '(?<=token=)[[:alnum:]]*')\"" > ${HOME}/.get-jupyter-url.sh && \
    echo "sh \${HOME}/.get-jupyter-url.sh" >> ${HOME}/.bashrc

### R development
# RUN pip install radian && \
#     R -q -e 'install.packages(c("markdown", "languageserver", "httpgd"), repos="cloud.r-project.org")' &&\
#     R -q -e 'remotes::install_github("ManuelHentschel/vscDebugger")'