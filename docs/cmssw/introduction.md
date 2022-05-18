# Introduction

CMSSW is a monster which contains a multitude of algorithms and tools,
all in one repository (not exactly a
[monorepo](https://en.wikipedia.org/wiki/Monorepo) but close).

Looking around for information on its structure and use you can very easily
get lost, frustrated, mad even, as most information is either too cryptic,
outdated or both. {==This document will try to focus on the few source files that
are of interest to the TkDPG group==}.

## CPU code

The main functionality of interest is located in the following CMSSW
subdirectories:

- `DataFormats/SiPixelDigi/`
- `DataFormats/SiPixelCluster/`
- `RecoLocalTracker/SiPixelClusterizer/`

To execute the CPU reconstruction code, you only need to run it on
any LXPLUS machine.

## GPU code

To speedup CPU code execution, a GPU version of (more or less) the same code
has been implemented in CUDA. 

!!! note
	
	If you're unfamiliar with GPU programming with CUDA, you are encouraged to
	follow [Angela's tutorial](../czangela-tutorial/index.md).

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
LXPLUS machine. See [here](working-with-cmssw/index.md) for instructions.



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
