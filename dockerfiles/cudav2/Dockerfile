FROM nvidia/cuda:11.2.0-cudnn8-devel-ubuntu20.04

LABEL maintainer="ceshine@ceshine.net"

# Based on https://github.com/anurag/fastai-course-1/

ARG PYTHON_VERSION=3.7
ARG CONDA_PYTHON_VERSION=3
ARG CONDA_DIR=/opt/conda
ARG USERNAME=docker
ARG USERID=1000

# Instal basic utilities
RUN apt-get update && \
  apt-get install -y --no-install-recommends git wget unzip bzip2 sudo build-essential && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

# Install miniconda
ENV PATH $CONDA_DIR/bin:$PATH
RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates && \
  wget https://repo.continuum.io/miniconda/Miniconda$CONDA_PYTHON_VERSION-latest-Linux-x86_64.sh -O /tmp/miniconda.sh && \
  echo 'export PATH=$CONDA_DIR/bin:$PATH' > /etc/profile.d/conda.sh && \
  /bin/bash /tmp/miniconda.sh -b -p $CONDA_DIR && \
  rm -rf /tmp/* && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

# Create the user
RUN useradd --create-home -s /bin/bash --no-user-group -u $USERID $USERNAME && \
  chown $USERNAME $CONDA_DIR -R && \
  adduser $USERNAME sudo && \
  echo "$USERNAME ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

USER $USERNAME
WORKDIR /home/$USERNAME

RUN conda install -y python=$PYTHON_VERSION && \
  conda install -y pytorch torchvision cudatoolkit=11.0 -c pytorch && \
  conda install -y h5py scikit-learn matplotlib seaborn \
  pandas mkl-service cython && \
  conda clean -tipsy

RUN  pip install --upgrade pip && \
  pip install pillow-simd && \
  rm -rf ~/.cache/pip

ENV CUDA_HOME=/usr/local/cuda
ENV CUDA_ROOT=$CUDA_HOME
ENV PATH=$PATH:$CUDA_ROOT/bin:$HOME/bin
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CUDA_ROOT/lib64
