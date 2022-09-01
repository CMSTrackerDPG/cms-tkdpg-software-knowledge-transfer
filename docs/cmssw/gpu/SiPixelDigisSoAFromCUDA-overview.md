# SiPixelDigisSoAFromCUDA.cc

Link on [github](https://github.com/cms-sw/cmssw/blob/CMSSW_12_4_6/EventFilter/SiPixelRawToDigi/plugins/SiPixelDigisSoAFromCUDA.cc).

Once [the CUDA Raw to Cluster code](SiPixelRawToClusterCUDA-overview.md)
has executed, the data is copied from the GPU back to the CPU in SoA format
using the `produce` function found in this file.
