# RCCL

ROCm Communication Collectives Library

## Introduction

RCCL (pronounced "Rickle") is a stand-alone library of standard collective communication routines for GPUs, implementing all-reduce, all-gather, reduce, broadcast, reduce-scatter, gather, scatter, and all-to-all. There is also initial support for direct GPU-to-GPU send and receive operations.  It has been optimized to achieve high bandwidth on platforms using PCIe, xGMI as well as networking using InfiniBand Verbs or TCP/IP sockets. RCCL supports an arbitrary number of GPUs installed in a single node or multiple nodes, and can be used in either single- or multi-process (e.g., MPI) applications.

The collective operations are implemented using ring and tree algorithms and have been optimized for throughput and latency. For best performance, small operations can be either batched into larger operations or aggregated through the API.

## Requirements

1. ROCm supported GPUs
2. ROCm stack installed on the system (HIP runtime & HCC or HIP-Clang)

## Quickstart RCCL Build

RCCL directly depends on HIP runtime, plus the HCC C++ compiler or the HIP-Clang compiler which are part of the ROCm software stack.
For ROCm installation instructions, see https://github.com/RadeonOpenCompute/ROCm.

The root of this repository has a helper script 'install.sh' to build and install RCCL on Ubuntu with a single command.  It does not take a lot of options and hard-codes configuration that can be specified through invoking cmake directly, but it's a great way to get started quickly and can serve as an example of how to build/install.

*  `./install.sh` -- builds library including unit tests
*  `./install.sh -i` -- builds and installs the library to /opt/rocm/rccl; installation path can be changed with --prefix argument (see below.)
*  `./install.sh -d` -- installs all necessary dependencies for RCCL.  Should be re-invoked if the build folder is removed.
*  `./install.sh -h` -- shows help
*  `./install.sh -t` -- builds library including unit tests
*  `./install.sh -r` -- runs unit tests (must be already built)
*  `./install.sh -p` -- builds RCCL package
*  `./install.sh -s` -- builds RCCL as a static library (default: shared)
*  `./install.sh -hcc` -- builds RCCL with hcc compiler; note that hcc is now deprecated. (default:hip-clang)
*  `./install.sh --prefix` -- specify custom path to install RCCL to (default:/opt/rocm)

## Manual build
#### To build the library :

```shell
$ git clone https://github.com/ROCmSoftwarePlatform/rccl.git
$ cd rccl
$ mkdir build
$ cd build
$ CXX=/opt/rocm/bin/hipcc cmake ..
$ make -j 8
```
You may substitute an installation path of your own choosing by passing CMAKE_INSTALL_PREFIX. For example:
```shell
$ CXX=/opt/rocm/bin/hipcc cmake -DCMAKE_INSTALL_PREFIX=$PWD/rccl-install ..
```
Note: ensure rocm-cmake is installed, `apt install rocm-cmake`.

#### To build the RCCL package and install package :

Assuming you have already cloned this repository and built the library as shown in the previous section:

```shell
$ cd rccl/build
$ make package
$ sudo dpkg -i *.deb
```

RCCL package install requires sudo/root access because it creates a directory called "rccl" under /opt/rocm/. This is an optional step and RCCL can be used directly by including the path containing librccl.so.

## Enabling peer-to-peer transport
In order to enable peer-to-peer access on machines with PCIe-connected GPUs, the HSA environment variable HSA_FORCE_FINE_GRAIN_PCIE=1 is required to be set, on top of requiring GPUs that support peer-to-peer access and proper large BAR addressing support.

## Tests

There are unit tests implemented with the Googletest framework in RCCL.  The unit tests require Googletest 1.10 or higher to build and execute properly (installed with the -d option to install.sh).
To invoke the unit tests, go to the build folder, then the test subfolder, and execute the appropriate unit test executable(s).

Unit test names are now of the format:

    [CollectiveCall]CorrectnessSweep/[CollectiveCall]CorrectnessTest.[Type of test]/[ncclRedOp_t]_[datatype]_[number of elements]_[number of devices]_[in place/out of place]_[environment variables]

This allows filtering of unit tests being run by their parameter values by passing the --gtest_filter command line flag, for example:

```shell
--gtest_filter="AllReduceCorrectnessSweep*float32*"
```
will run only AllReduce correctness tests with float32 datatype. See "Running a Subset of the Tests" at https://chromium.googlesource.com/external/github.com/google/googletest/+/HEAD/googletest/docs/advanced.md for more information on how to form more advanced filters.


There are also other performance and error-checking tests for RCCL.  These are maintained separately at https://github.com/ROCmSoftwarePlatform/rccl-tests.
See the rccl-tests README for more information on how to build and run those tests.

## Library and API Documentation

Please refer to the [Library documentation](https://rccl.readthedocs.io/) for current documentation.

## Copyright

All source code and accompanying documentation is copyright (c) 2015-2021, NVIDIA CORPORATION. All rights reserved.

All modifications are copyright (c) 2019-2021 Advanced Micro Devices, Inc. All rights reserved.
