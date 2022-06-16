# SiPixelRawToClusterGPUKernel

File containing the `pixelgpudetails` namespace which in turn contains:

- A class of the same name  which,
  among other things, contains:
	- The `makeClustersAsync` method
- CUDA kernels:
	- `RawToDigi_kernel`


Files on github: [header](https://github.com/cms-sw/cmssw/blob/master/RecoLocalTracker/SiPixelClusterizer/plugins/SiPixelRawToClusterGPUKernel.h) and [source](https://github.com/cms-sw/cmssw/blob/master/RecoLocalTracker/SiPixelClusterizer/plugins/SiPixelRawToClusterGPUKernel.cu).



## `SiPixelRawToClusterGPUKernel` Class

An instance of this class is created and called by the
[`SiPixelRawToClusterCUDA`](SiPixelRawToClusterCUDA-overview.md)) class.

### `makeClustersAsync`

A function that implements the following functionality:

- Converts Raw Pixel data to Digis (by calling the `RawToDigi_kernel`)
- Calibrates the Digis (by calling the [`calibDigis.md`](gpuCalibPixel-calibDigis.md) kernel)
- {==Counts modules(????)==} (by calling the
[`countModules`](gpuClustering-countModules.md) kernel).
- Uses the Digis created in the first step to create Clusters (by calling
the [`findClus`](gpuClustering-findClus.md) kernel)

#### Flowchart

```mermaid
graph TB
	
	subgraph makeClustersAsync
		C[RawToDigi_kernel] --> D[calibDigis]
		D --> E[countModules]
		E --> F[findClus]
	
		style C fill:lightgreen
		style D fill:lightgreen	
		style E fill:lightgreen	
		style F fill:lightgreen	
	end
	
	subgraph Legend
		A[CUDA Kernel]
		style A fill:lightgreen
		style Legend fill:none
	end
```
