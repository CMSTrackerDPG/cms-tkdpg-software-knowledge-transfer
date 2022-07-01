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

### UML diagram
!!! warning

	UML diagram incomplete
	
```mermaid
classDiagram
class SiPixelRawToClusterGPUKernel{
-uint32_t nDigis 
-cms::cuda::host::unique_ptr~uint32_t[]~ nModules_Clusters_h
-SiPixelDigisCUDA digis_d
-SiPixelClustersCUDA clusters_d
+makeClustersAsync(...) void
+makePhase2ClustersAsync(...) void
+getResults()
+getErrors() 
}
```

### Attributes

#### `digis_d`

An instance of [SiPixelDigisCUDA](SiPixelDigisCUDA.md), which stores digi
information on the CUDA device (hence the `_d` in the name).

It is initialized using the `numDigis` and the CUDA `stream` as parameters.

#### `clusters_d`

An instance of [SiPixelClustersCUDA](SiPixelClustersCUDA.md) which is used
to store clusters found during the [`findClus`](gpuClustering-findClus.md) kernel execution

### Methods

#### `makeClustersAsync`

A function that implements the following functionality:

- Converts Raw Pixel data to Digis (by calling the `RawToDigi_kernel`)
- Calibrates the Digis (by calling the [`calibDigis.md`](gpuCalibPixel-calibDigis.md) kernel)
- {==Counts modules(????)==} (by calling the
[`countModules`](gpuClustering-countModules.md) kernel).
- Uses the Digis created in the first step to create Clusters (by calling
the [`findClus`](gpuClustering-findClus.md) kernel)

##### Flowchart

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
