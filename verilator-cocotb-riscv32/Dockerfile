#-------------------------------------------------------
# Build Verilator + Cocotb + Riscv gnu 32 bit toolchain
#-------------------------------------------------------
FROM siliconbootcamp/verilator-cocotb:latest

ARG DEBIAN_FRONTEND=noninteractive
LABEL maintainer="ninja@siliconbootcamp.com"
LABEL version="1.0"

SHELL ["/bin/bash", "-c"]
USER root:root

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive \
    &&  apt-get install --no-install-recommends -y \
    autoconf automake autotools-dev curl python3 \
    libmpc-dev libmpfr-dev libgmp-dev gawk \
    build-essential bison flex texinfo gperf \
    libtool patchutils bc zlib1g-dev libexpat-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /tmp

ENV PATH=$PATH:/opt/riscv32/bin

RUN git clone https://github.com/riscv/riscv-gnu-toolchain \
    && cd /tmp/riscv-gnu-toolchain \
    && mkdir /opt/riscv32 \
    && mkdir build \
    && cd build \
    &&  ../configure --prefix=/opt/riscv32 --with-arch=rv32im --with-abi=ilp32 \
    && make

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive \
    &&  apt-get install --no-install-recommends -y \
    bsdmainutils \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /work

ENTRYPOINT ["/bin/bash"]


