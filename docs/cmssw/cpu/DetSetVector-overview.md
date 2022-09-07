# DetSetVector

File on [github](https://github.com/cms-sw/cmssw/blob/master/DataFormats/Common/interface/DetSetVector.h).

Defined in the `edm` namespace, this is a template which creates
a vector which groups data per module it belongs to. It works as an
iterator, with each "row" being a vector of type `T`.
Each "row" is, therefore, associated with a module.

An example is a `DetSetVector` of `PixelDigi`s
(see [here](../gpu/SiPixelDigisClustersFromSoA-overview.md)).

!!! warning

	Not to be confused with an `edmNew::DetSetVector`, a.k.a `DetSetVectorNew`.
