# Overview

"Pixel-local reconstruction" refers to the chain of CMSSW modules which
are reponsible for converting raw CMS detector Pixel data to [Vertices](../../basic-concepts.md#vertex).

```mermaid
graph LR
  A[Raw data] --> B[Digis];
  B --> C[Clusters]
  C --> D[RecHits]
  D --> E[Tracks]
  E --> F[Vertices]
```
