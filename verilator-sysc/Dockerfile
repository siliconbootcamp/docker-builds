#-------------------------------------------------
# Build Verilator + SystemC
#-------------------------------------------------
FROM ubuntu:20.04

ARG DEBIAN_FRONTEND=noninteractive
LABEL maintainer="ninja@siliconbootcamp.com"
LABEL version="1.0"
LABEL description="SystemC (SC) libraries and SystemC verification (SCV) libraries. Comes with compiler and runtime environment."

SHELL ["/bin/bash", "-c"]
USER root:root

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive \
    && apt-get install --no-install-recommends -y \
                        build-essential \
                        cmake \
                        g++ \
                        wget \
                        perl=5.* \
                        ca-certificates \
                        make gcc g++ \
                        autoconf \
                        bc \
                        bison \
                        build-essential \
                        ca-certificates \
                        ccache \
                        flex \
                        git \
                        libfl-dev \
                        libgoogle-perftools-dev \
                        perl \
                        python3 python3-dev python3-pip \
                        libpcre3 libpcre3-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ARG SYSTEMC_VERSION=2.3.3
ARG SCV_VERSION=2.0.1

ENV SRC_DIR=/usr/src
ENV SYSTEMC_VERSION=${SYSTEMC_VERSION}
ENV SCV_VERSION=${SCV_VERSION}

ENV CC=gcc
ENV CXX=g++
ENV SYSTEMC_INSTALL_PATH=/opt/systemc-${SYSTEMC_VERSION}
#ENV SCV_INSTALL_PATH=/opt/scv-${SCV_VERSION}
ENV SCV_INSTALL_PATH=${SYSTEMC_INSTALL_PATH}
ENV CPLUS_INCLUDE_PATH=${CPLUS_INCLUDE_PATH}:${SYSTEMC_INSTALL_PATH}/include
ENV LIBRARY_PATH=${LIBRARY_PATH}:${SYSTEMC_INSTALL_PATH}/lib-linux64
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${SYSTEMC_INSTALL_PATH}/lib-linux64
ENV SYSTEMC_HOME=${SYSTEMC_INSTALL_PATH}
ENV SYSTEMC=${SYSTEMC_INSTALL_PATH}

RUN apt update -qq \
    && apt install --no-install-recommends -qq -y \

    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p ${SRC_DIR}
# Fetch and build SystemC core library (includes TLM).
WORKDIR ${SRC_DIR}
RUN wget https://www.accellera.org/images/downloads/standards/systemc/systemc-${SYSTEMC_VERSION}.tar.gz \
    && tar -xf ./systemc-${SYSTEMC_VERSION}.tar.gz \
    && rm -f ./systemc-${SYSTEMC_VERSION}.tar.gz \
    && cd ./systemc-${SYSTEMC_VERSION} \
    && ./configure --prefix=${SYSTEMC_INSTALL_PATH} \
    && make \
    && make install \
    && make check \
    && rm -rf ${SRC_DIR}/*

# Fetch and build SystemC regression tests.
#WORKDIR ${SRC_DIR}
#RUN wget https://www.accellera.org/images/downloads/standards/systemc/systemc-regressions-${SYSTEMC_VERSION}.tar.gz \
#    && tar -xf ./systemc-regressions-${SYSTEMC_VERSION}.tar.gz \
#    && cd ./systemc-regressions-${SYSTEMC_VERSION}/tests \
#    && ../scripts/verify.pl * \
#    && rm -rf ${SRC_DIR}/*

# Fetch and build SystemC verification library.
WORKDIR ${SRC_DIR}
RUN wget https://www.accellera.org/images/downloads/standards/systemc/scv-${SCV_VERSION}.tar.gz \
    && tar -xf ./scv-${SCV_VERSION}.tar.gz \
    && cd ./scv-${SCV_VERSION} \
    && ./configure --prefix=${SCV_INSTALL_PATH} --with-systemc=${SYSTEMC_INSTALL_PATH} \
    && make \
    && make install \
    && make check \
    && rm -rf ${SRC_DIR}/*

ENV SYSTEMC_INCLUDE=${SYSTEMC_HOME}/include
ENV SYSTEMC_LIBDIR=${SYSTEMC_HOME}/lib-linux64

ARG REPO=https://github.com/verilator/verilator

#Last stable tag on 9/30/2020
ARG SOURCE_COMMIT=v4.100

#ARG SOURCE_COMMIT=master

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

ENTRYPOINT ["/bin/bash"]

