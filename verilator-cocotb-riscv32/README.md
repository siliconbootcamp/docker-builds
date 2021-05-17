Dockerfile for Verilator + Cocotb + gnu toolchain for RISCV 32 bit (RV32I)

The GNU toolchain is to compile RISCV assembly or C programs into RISCV machine code.

This docker image is the starting point for RISCV RTL simulations using verilator and cocotb.

Mount your local directory with RISCV RTL and cocotb testbench using:
docker run -it -v $PWD:/work siliconbootcamp/verilator-cocotb-riscv32:latest

