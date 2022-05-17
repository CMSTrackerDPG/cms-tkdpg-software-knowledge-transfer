# Introduction

CMSSW is a monster which contains a multitude of algorithms and tools,
all in one repository (not exactly a
[monorepo](https://en.wikipedia.org/wiki/Monorepo) but close).

Looking around for information on its structure and use you can very easily
get lost, frustrated, mad even, as most information is either too cryptic,
outdated or both. {==This document will try to focus on the very few files that
are of interest to the TkDPG group==}.

## CPU

### Overview

The main functionality of interest is located in the following CMSSW
subdirectories:

- `DataFormats/SiPixelDigi/`
- `DataFormats/SiPixelCluster/`
- `RecoLocalTracker/SiPixelClusterizer/`

## GPU

### Overview

To speedup CPU code execution, a GPU version of (more or less) the same code
has been implemented in CUDA. 

!!! note 

	Throughout this document, keep in mind that there is no one-to-one mapping 
	of the CPU functions to CUDA kernels, not even structure-wise.
	
The main functionality of interest is located in the following CMSSW
subdirectories:

- `DataFormats/SiPixelDigi/`
- `RecoLocalTracker/SiPixelClusterizer/`	

### Running GPU code

To run an execute code on GPUs, you must first connect to the appropriate
LXPLUS machine. See [here](working-with-cmssw/index.md) for instructions.
