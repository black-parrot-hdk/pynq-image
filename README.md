# pynq-image
This repo is used to build custom SD card images for PYNQ based boards PYNQ-Z2 and Ultra96v2.

## Prerequisites
### Ubuntu 18.04
This following packages are needed.

    sudo dpkg --add-architecture i386
    sudo apt-get install lib32stdc++6 libgtk2.0-0:i386 libfontconfig1:i386 libx11-6:i386 libxext6:i386 libxrender1:i386 libsm6:i386 libqtgui4:i386 libxtst6:i386 libxtst6
    sudo apt-get install sudo gcc python python3 libncurses5-dev libncursesw5-dev zlib1g:i386 pv wget gawk less git
    sudo apt-get install net-tools xterm autoconf libtool texinfo zlib1g-dev gcc-multilib build-essential

### Xilinx tools
This flow is based on the v2.6.0 of PYNQ that needs Vivado, Vitis, and PetaLinux version 2020.1 for compatiblity.

### PetaLinux cache
During the build PetaLinux needs to download data in the form of the architecure-dependent `sstate-cache` and the architecture-agnostic `downloads` directories.
To avoid redoing this every time we run the build we can pre-download the directories from <a href="https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/embedded-design-tools/2020-1.html" target="_blank">here</a>.
The `README` file in the Xilinx download page describes how to extract the files and where to put them in the PetaLinux install directory.

### Prebuilt board-agnostic Linux image
To save time on the build process, we need to download the prebuilt Linux image v2.6(<a href="http://www.pynq.io/board.html" target="_blank">here</a>) for the board's ARM architecture(`arm` for PYNQ-Z2 and `aarch64` for Ultra96v2).
Using the PREBUILT flag to point to this image will skip the the board-agnostic stage of the build.

### PYNQ source distribution
To avoid rebuilding the PYNQ source distribution package, and consequently bypass the need to build bitstreams and MicroBlazes’ bsps and binaries,
we need to download a prebuilt PYNQ sdist tarball v2.6 from the <a href="https://github.com/Xilinx/PYNQ/releases" target="_blank">PYNQ releases section</a>.
The PYNQ_SDIST flag is used during build to point to this tarball.

## Building the Ultra96v2 custom BSP
On our Ultra96v2 boards, we have enabled a anti-freezing mechanism using OS based watchdog timers, so before building the final image we need to build its custom BSP file.
To do this go into the Ultra96-PYNQ submodule and follow the instructions in `README` on "Creating your own BSP".

## Build
Before building the image make sure all the prerequisites are present and make sure you have a few hours, 50-100 GBs of free disk space, and enough RAM and CPU.
### Setup the environment
Last thing before build is setup the build environment by running the `setup_host.sh` script at `PYNQ/sdbuild/scripts`.
On some systems you have to run `sudo update-binfmts --display` to make sure that qemu for your ARM architecure is marked as enabled. If not, manually enable it
by running for example `sudo update-binfmts --enable qemu-arm`.
### PYNQ-Z2
To build the image for PYNQ-Z2 go into `PYNQ/sdbuild` and run:

    make BOARDS=Pynq-Z2 PREBUILT=<path to bionic.arm.<version>.img> PYNQ_SDIST=<path to pynq-<version>.tar.gz>
### Ultra96v2
To build the image for PYNQ-Z2 go into `PYNQ/sdbuild` and run:

    make BOARDDIR=<path to Ultra96-PYNQ submodule> PREBUILT=<path to bionic.aarch64.<version>.img> PYNQ_SDIST=<pynq-<version>.tar.gz>
 
 ## Changing CMA size
 If you need to change the CMA memory size for allocating larger memories during benchmarks runs, you can change it at `PYNQ/sdbuild/boot/meta-pynq/recipes-kernel/linux/linux-xlnx/pynq.cfg` before running the build.
 The current CMA size is set to 256MB.
