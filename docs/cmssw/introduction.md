# Introduction to CMSSW

CMSSW is a monster which contains a multitude of algorithms and tools,
all in one repository (not exactly a
[monorepo](https://en.wikipedia.org/wiki/Monorepo) but close).

Looking around for information on its structure and use you can very easily
get lost, frustrated, mad even, as most information is either too cryptic,
outdated or both. {==This document will try to focus on the few source files that
are of interest to the TkDPG group==}.

## Usage

The usual *modus operandi* with CMSSW is the following:

- Clone one of the available versions of CMSSW.
- Checkout packages that you want to make changes to.
- Make changes to any file you want.
- Build CMSSW again.
- Execute [Workflows](working-with-cmssw/workflows/overview.md) and validation.

To do so, it is recommended that you work with CMSSW on LXPLUS, where many 
useful tools for managing, code-checking, code-formatting, building are available there.
You can find instructions on how to setup your work environment and get started 
in the [Developing section](working-with-cmssw/software.md).

## Versions

CMSSW is regularly released and built, both from stable and and unstable version. 
More information can be found in the [Build Types section](build-types.md).

## CPU code

The main functionality of interest is located in the following CMSSW
subdirectories:

- `DataFormats/SiPixelDigi/`
- `DataFormats/SiPixelCluster/`
- `RecoLocalTracker/SiPixelClusterizer/`

To execute the CPU reconstruction code, you can do so by running it on
any LXPLUS machine, usually through [Workflows](working-with-cmssw/workflows/overview.md).

Information about the CPU code is found in the [CPU section](cpu/index.md).

## GPU code

To speedup CPU code execution, a GPU version of (more or less) the same code
has been implemented in CUDA. 

!!! note
	
	If you're unfamiliar with GPU programming with CUDA, you are encouraged to
	follow [Angela's tutorial](../czangela-tutorial/index.md). Apart from
	a general primer to CUDA code, it also provides	insights on the GPU code 
	used in CMSSW.

!!! note

	Throughout this document, keep in mind that there is no one-to-one mapping 
	of the CPU functions to CUDA kernels, not even structure-wise.
	
The main functionality of interest is located in the following CMSSW
subdirectories:

- `DataFormats/SiPixelDigi/`
- `CUDADataFormats/SiPixelCluster/`
- `CUDADataFormats/SiPixelDigi/`
- `RecoLocalTracker/SiPixelClusterizer/`

To run and execute code on GPUs, you must first connect to the appropriate
LXPLUS machine. See the [Connecting to GPU machines section](working-with-cmssw/gpu-machines.md) 
for instructions.

Information about the GPU code is found in the [GPU section](gpu/index.md).

## Alpaka code

!!! todo

	Add links, info
	
!!! warning
	
	Possibly inaccurate links/description
	
An [ongoing effort](https://github.com/cms-patatrack/pixeltrack-standalone) by the
Patatrack team aims to port reconstruction code to the 
 Alpaka framework[^1][^2],
a framework which aims to abstract away from the hardware that a piece of code runs on (CPU/GPU), so
that a single source file can run on any of the supported hardware.

Alpaka resembles CUDA code, and has (more or less) the same logic with
[frameworks like OpenCL](https://alpaka.readthedocs.io/en/0.5.0/usage/intro.html#similar-projects).

[^1]: [Alpaka's readthedocs](https://alpaka.readthedocs.io/en/0.5.0/usage/intro.html)
[^2]: [Alapaka group's homepage](https://alpaka-group.github.io/alpaka/)
