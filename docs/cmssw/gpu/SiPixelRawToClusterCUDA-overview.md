# SiPixelRawToClusterCUDA.cc

File containing the wrapper class which wraps the [`SiPixelRawToClusterGPUKernel`](SiPixelRawToClusterGPUKernel-overview.md) class.

File on [github](https://github.com/cms-sw/cmssw/blob/master/RecoLocalTracker/SiPixelClusterizer/plugins/SiPixelRawToClusterCUDA.cc).

## UML diagram

```mermaid
 classDiagram
	  class SiPixelRawToClusterCUDA{
	 	 -SiPixelRawToClusterGPUKernel gpuAlgo_
      }
	  
	  class SiPixelRawToClusterGPUKernel{
	 	 -makeClustersAsync()
		 -makePhase2ClustersAsync()
      }
	  
     SiPixelRawToClusterCUDA o-- SiPixelRawToClusterGPUKernel
```

## Class attributes

### `gpuAlgo_`

An instance of [`SiPixelRawToClusterGPUKernel`](SiPixelRawToClusterGPUKernel-overview.md)
