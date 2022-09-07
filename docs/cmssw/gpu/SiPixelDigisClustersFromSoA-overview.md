# SiPixelDigisClustersFromSoA

Link on [github](https://github.com/cms-sw/cmssw/blob/CMSSW_12_4_6/RecoLocalTracker/SiPixelClusterizer/plugins/SiPixelDigisClustersFromSoA.cc).

After the [CUDA to SoA conversion](SiPixelDigisSoAFromCUDA-overview.md) has executed,
the data (which is in [`SiPixelDigisCUDASOA`](SiPixelDigisCUDASOAView.md) format)
is copied to `edm::DetSetVector`s (of [`PixelDigi`s](../cpu/PixelDigi-overview.md)
and `edmNew::DetSetVector`s of [`SiPixelCluster`s](../cpu/SiPixelCluster-overview.md))

```mermaid
flowchart
	cudasoa[SiPixelDigisCUDASOA] --> detsetpix[edm::DetSetVector of PixelDigi]
	cudasoa --> detsetclus[edmNew::DetSetVectorNew of SiPixelCluster]	

```

!!! warning
	
	- For clusters (stored in the `outputClusters` variable), an  `edmNew::DetSetVector<SiPixelCluster>`
	is used (a.k.a `SiPixelClusterCollectionNew`, found in
	[`SiPixelCluster.h`](../cpu/SiPixelCluster-overview.md)).
	- For pixel digis (stored in the `collection` variable), an `edm::DetSetVector<PixelDigi>` 
	is used (see [`PixelDigi`s](../cpu/PixelDigi-overview.md)). 
