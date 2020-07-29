# SymbiFlow examples

This repository provides example FPGA designs that can be built using the SymbiFlow open source toolchain.
The examples target the Xilinx Artix-7 and the QuickLogic EOS S3 devices.

The repository includes:

* [eos-s3](./eos-s3) - Example FPGA designs for the QuickLogic EOS S3 series of parts:

  * Verilog code
  * Pin constraints files
  * Timing constraints files
  * Makefiles for running the SymbiFlow toolchain

* [xc7](./xc7) - Example FPGA designs for the Xilinx 7 series of parts:

  * Verilog code
  * Pin constraints files
  * Timing constraints files
  * Makefiles for running the SymbiFlow toolchain

* [.travis.yml](.travis.yml) - Travis CI configuration file

## Toolchain installation

This section describes how to install the toolchain. This procedure is divided in two steps:

- Installing the Conda package manager
- Downloading the architecture definitions and installing the toolchain

1. Conda

```bash
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O conda_installer.sh
```

2. Toolchain

FIXME: This should be included from xc7/README.md

For the Artix-7 devices:

```bash
INSTALL_DIR="/opt/symbiflow/xc7"
bash conda_installer.sh -b -p $INSTALL_DIR/conda && rm conda_installer.sh
source "$INSTALL_DIR/conda/etc/profile.d/conda.sh"
conda env create -f xc7/environment.yml
conda activate xc7
wget -qO- https://storage.googleapis.com/symbiflow-arch-defs/artifacts/prod/foss-fpga-tools/symbiflow-arch-defs/presubmit/install/477/20200714-082108/symbiflow-arch-defs-install-8eb88e76.tar.xz | tar -xJ --one-top-level=$INSTALL_DIR/install
conda deactivate
```

FIXME: This should be included from eos-s3/README.md

For the EOS S3 devices:

```bash
export INSTALL_DIR="/opt/symbiflow/eos-s3"
wget 'https://github.com/QuickLogic-Corp/quicklogic-fpga-toolchain/releases/download/v1.1.0/Symbiflow_v1.1.0.gz.run'
bash Symbiflow_v1.1.0.gz.run
```

## Build Example Designs

With the toolchain installed, you can build the example designs.
The example designs are provided in separate directories:

* `xc7` directory for the Artix-7 devices
* `eos-s3` directory for the EOS S3 devices

### Example designs for the Artix-7 devices:


1. `counter` - simple 4-bit counter driving LEDs. The design targets the [Basys3 board](https://store.digilentinc.com/basys-3-artix-7-fpga-trainer-board-recommended-for-introductory-users/) and the [Arty board](https://store.digilentinc.com/arty-a7-artix-7-fpga-development-board-for-makers-and-hobbyists/).
1. `picosoc` - [picorv32](https://github.com/cliffordwolf/picorv32) based SoC. The design targets the [Basys3 board](https://store.digilentinc.com/basys-3-artix-7-fpga-trainer-board-recommended-for-introductory-users/).
1. `linux_litex` - [LiteX](https://github.com/enjoy-digital/litex) based system with Linux capable [VexRiscv core](https://github.com/SpinalHDL/VexRiscv). The design includes [DDR](https://github.com/enjoy-digital/litedram) and [Ethernet](https://github.com/enjoy-digital/liteeth) controllers. The design targets the [Arty board](https://store.digilentinc.com/arty-a7-artix-7-fpga-development-board-for-makers-and-hobbyists/).

The Linux images for the `linux_litex` example can be built following the [linux on litex vexriscv](https://github.com/litex-hub/linux-on-litex-vexriscv) instructions.
The `linux_litex` example is already provided with working Linux images.

To build the examples, run the following commands:

```bash
export INSTALL_DIR="/opt/symbiflow/xc7"
# adding symbiflow toolchain binaries to PATH
export PATH="$INSTALL_DIR/install/bin:$PATH"
source "$INSTALL_DIR/conda/etc/profile.d/conda.sh"
conda activate xc7
git clone https://github.com/SymbiFlow/symbiflow-examples && cd symbiflow-examples
# counter example
pushd xc7/counter_test && TARGET="arty_50" make && popd
pushd xc7/counter_test && TARGET="arty_100" make && popd
pushd xc7/counter_test && make clean && TARGET="basys3" make && popd
# picosoc example
pushd xc7/picosoc_demo && make && popd
# litex example
wget https://raw.githubusercontent.com/enjoy-digital/litex/master/litex_setup.py
chmod +x litex_setup.py
./litex_setup.py init
./litex_setup.py install
wget https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.1.0-2019.01.0-x86_64-linux-ubuntu14.tar.gz
tar -xf riscv64-unknown-elf-gcc-8.1.0-2019.01.0-x86_64-linux-ubuntu14.tar.gz
export PATH=$PATH:$PWD/riscv64-unknown-elf-gcc-8.1.0-2019.01.0-x86_64-linux-ubuntu14/bin/
pushd litex/litex/boards/targets && ./arty.py --toolchain symbiflow --cpu-type vexriscv --build && popd
# linux litex example
pushd xc7/linux_litex_demo && make && popd
```

### Example design for the EOS S3 devices:

FIXME: This should be included from eos-s3/README.md

1. `btn_counter` - simple 4-bit counter driving LEDs. The design targets the [EOS S3 FPGA](https://www.quicklogic.com/products/eos-s3/).

To build the example, run the following commands:

```bash
export INSTALL_DIR="/opt/symbiflow/eos-s3"
export PATH="$INSTALL_DIR/install/bin:$INSTALL_DIR/install/bin/python:$PATH"
source "$INSTALL_DIR/conda/etc/profile.d/conda.sh"
conda activate

git clone https://github.com/SymbiFlow/symbiflow-examples && cd symbiflow-examples
pushd eos-s3 && make && popd
```
