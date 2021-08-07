#-------------------------------------------------
# Build Verilator + Cocotb
#-------------------------------------------------
FROM ubuntu:20.04

ARG DEBIAN_FRONTEND=noninteractive
LABEL maintainer="ninja@siliconbootcamp.com"
LABEL version="1.0"

SHELL ["/bin/bash", "-c"]
USER root:root

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive \
    && apt-get install --no-install-recommends -y \
                        emacs vim \
                        make gcc g++ \
                        autoconf \
                        bc \
                        bison \
                        build-essential \
                        ca-certificates \
                        ccache \
                        flex \
                        wget \
                        git \
                        libfl-dev \
                        libgoogle-perftools-dev \
                        perl \
                        python3 python3-dev python3-pip \
                        libpcre3 libpcre3-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

#Install Swig
RUN mkdir /swig-build && cd /swig-build && \
    wget http://prdownloads.sourceforge.net/swig/swig-4.0.2.tar.gz \
    && tar zxf swig-4.0.2.tar.gz && cd swig-4.0.2 && \
    ./configure && make && make install && rm -rf /swig-build

#Cocotb install
RUN pip3 install cocotb

#Need numpy
RUN pip3 install numpy

#Dump package, superset of pickle
RUN pip3 install dill

#Install pyTorch
RUN pip3 install torch torchvision

ARG REPO=https://github.com/verilator/verilator

#Last stable tag on 9/30/2020
#ARG SOURCE_COMMIT=v4.100

ARG SOURCE_COMMIT=stable

WORKDIR /tmp

# Add an exception for the linter, we want to cd here in one layer
# to reduce the number of layers (and thereby size).
# hadolint ignore=DL3003
RUN git clone "${REPO}" verilator && \
    cd verilator && \
    git checkout "${SOURCE_COMMIT}" && \
    autoconf && \
    ./configure && \
    make -j "$(nproc)" && \
    make install && \
    cd .. && \
    rm -r verilator

WORKDIR /work

#Set environment variables
ENV COCOTB_REDUCED_LOG_FMT 1
ENV SIM verilator
ENV NO_COLOR 1

ENTRYPOINT ["/bin/bash"]


