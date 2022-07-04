# gpuClustering overview

Located in: `RecoLocalTracker/SiPixelClusterizer/plugins/`

Link to file on
[github](https://github.com/cms-sw/cmssw/blob/master/RecoLocalTracker/SiPixelClusterizer/plugins/gpuClustering.h).

## Purpose

This file's purpose is to provide a namespace (`gpuclustering`) which
contains the following functionality in the form of CUDA kernels:

- Count modules ({==TODO???==}) (see [here](gpuClustering-countModules.md))
- Find clusters in the data passed (see [here](gpuClustering-findClus.md))
