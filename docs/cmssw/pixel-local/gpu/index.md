# GPU code overview

CMSSW modules meant to be run on NVIDIA GPUs.

Functionality covered in this documentation:

- [Raw to Digi conversion](SiPixelRawToClusterCUDA-overview.md)
- [Clusterization](SiPixelRawToClusterCUDA-overview.md)

## Module Dataflow

```mermaid
flowchart TD
 classDef Module fill:#c5c5FF,stroke:#0000ff,stroke-width:4px,color:#0000ff;
 classDef CUDAProduct fill:#ffffff,stroke:#008000,stroke-width:2px,color:#008000; 
 SiPixelRawToClusterCUDA --> SiPixelDigisCUDA([SiPixelDigisCUDA])
 SiPixelRawToClusterCUDA --> SiPixelClustersCUDA([SiPixelClustersCUDA])
 
 SiPixelClustersCUDA --> SiPixelRecHitCUDA
 SiPixelDigisCUDA --> SiPixelRecHitCUDA 
 SiPixelRecHitCUDA --> TrackingRecHit2DGPU([TrackingRecHit2DGPU])
 
 TrackingRecHit2DGPU --> CAHitNtupletCUDA
 CAHitNtupletCUDA --> PixelTrackHeterogeneous([PixelTrackHeterogeneous])
 PixelTrackHeterogeneous --> PixelVertexProducerCUDA
 PixelVertexProducerCUDA --> ZVertexHeterogeneous([ZVertexHeterogeneous])

 subgraph Legend
  CUDAProduct([CUDA Product])
  CMSSWModule([CMSSW Module])
 end
  
 class CMSSWModule,SiPixelRawToClusterCUDA,SiPixelRecHitCUDA,CAHitNtupletCUDA,PixelVertexProducerCUDA Module;
 class CUDAProduct,SiPixelClustersCUDA,SiPixelDigisCUDA,TrackingRecHit2DGPU,PixelTrackHeterogeneous,ZVertexHeterogeneous CUDAProduct;  
```

## Data Structure

The [SoA](../../../basic-concepts.md#soaaos) approach is used to store
pixel data used by the CUDA code.

In short, data from each [module](../../../basic-concepts.md#module)
is concatenated into multiple 1D arrays.

For example, suppose we have two modules (module0 and module1).
Each one will contain 16 x 80 x 52 = 66560 pixels. Each pixel has
its module coordinates (`x` and `y`) and the module index it belongs
to (`moduleInd`, for this example `0` and `1`).

A simplistic visualization of the example above can be seen
below; each module is composed of 2 x 8
[ROCs](../../../basic-concepts.md#read-out-chip), each pixel having
unique coordinates relative to the module.

![Module layout](img/module_layout.png)

The SoA approach to store the pixel data would look like
the image below; data from all modules are concatenated one after
another in 1D arrays:

![SoA Data layout](img/soa_layout.png){ width="500" }

!!! warning

    The data is **not** stored in a per-module sorted manner,
    meaning that `module1` data could precede `module0`'s data.
    Data from each module *is*, however, stored consecutively,
    meaning that data from one module is not split up into several
    blocks.

An actual example of such arrays can be seen in the
[SiPixelDigisCUDASOAView](./SiPixelDigisCUDASOAView.md).

!!! todo

    Where is this data structure created? Unpacking?
