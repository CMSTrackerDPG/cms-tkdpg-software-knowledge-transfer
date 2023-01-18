# Introduction to CMSSW

CMSSW is [a monster](../basic-concepts.md#cmssw) which contains a multitude of algorithms and tools,
all in one repository.

Looking around for information on its structure and use you can very easily
get lost, frustrated, mad even, as most information is either too cryptic,
outdated or both. {==This document will try to focus on the few source files that
are of interest to the TkDPG group==}.

A usual split of the codebase is based on the hardware the sowftware
is targeted:

- [CPU code](#cpu-code) targets CPU-only execution.
- [GPU code](#gpu-code) targets exexution on NVIDIA GPU-capable systems.


## Usage

The usual way to work with CMSSW is the following (from within [one of the available machines of your choice](available_machines.md)):

- [Connect](available_machines.md) to an LXPLUS (CPU only) or P5 (with GPUs) machine.
- [List](setup.md#search-for-available-releases) the available CMSSW versions.
- [Clone](setup.md#create-a-cmssw-area) one of the available versions of CMSSW. 
See [Releases](releases.md) for more information on the different releases.
- [Checkout packages](setup.md#checkout-a-few-packages-using-git-cms-addpkg)
that you want to make changes to.
- Make changes to any file you want.
- [Build](build.md) CMSSW again.
- Execute [Workflows](workflows/overview.md) in order to do [validation](validation.md).
- Measure your code's [throughput](throughput.md).
- [Open a PR](proposing-changes.md) to cmssw.

To do so, it is recommended that you work with CMSSW on an [appropriately configured machine](available_machines.md), where many 
useful tools for managing, code-checking, code-formatting, building are available.

See also: [Setup](setup.md).

## Versions

CMSSW is regularly released and built, both from stable and and unstable version.
More information can be found in the [Releases section](releases.md).

## CPU code

For Pixel Track Reconstruction, the main functionality of interest is located in the following CMSSW
subdirectories:

- `DataFormats/SiPixelDigi/`
- `DataFormats/SiPixelCluster/`
- `RecoLocalTracker/SiPixelClusterizer/`

To execute the CPU reconstruction code, you can do so by running it on
any LXPLUS machine, usually through [Workflows](workflows/overview.md).

Information about the CPU code is found in the [CPU section](pixel-local/cpu/index.md).

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
	
For Pixel Tracks, the main functionality of interest is located in the following CMSSW
subdirectories:

- `CUDADataFormats/SiPixelDigi/`
- `CUDADataFormats/SiPixelCluster/`
- `CUDADataFormats/TrackingRecHit/`
- `CUDADataFormats/Track/`
- `CUDADataFormats/Vertex/`
- `RecoLocalTracker/SiPixelClusterizer/`
- `RecoLocalTracker/SiPixelClusterizer/`
- `RecoLocalTracker/SiPixelRecHits/`
- `RecoTracker/TkSeedGenerator/`
- `RecoPixelVertexing/PixelTriplets`
- `RecoPixelVertexing/PixelTrackFitting/`
- `RecoPixelVertexing/PixelVertexFinding/`

To run and execute code on GPUs, you must first connect to the appropriate
LXPLUS machine. See the [here](available_machines.md) 
for instructions.

Information about the GPU code is found in the [GPU section](pixel-local/gpu/index.md).

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
