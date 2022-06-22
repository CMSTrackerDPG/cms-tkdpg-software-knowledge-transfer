# Basic concepts

Before diving deeper into the software itself, some terms and concepts must be covered
so that the reader does not get lost in the abundance of terminology
and jargon that pervades anything CERN-related.

Your companion glossary can always be found
[here](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookGlossary).

!!! warning

	There might be lots of inaccuracies in this document. Don't judge me!
## Hardware

### Pixel Detector

!!! todo
	
	TODO
	
[Comprised of many Pixels](https://cms.cern/detector/identifying-tracks/silicon-pixels), 
the Pixel Detector is part of the whole CMS detector.


In the reconstruction code, the Pixel Detector is represented as a 2D matrix of
pixels (`PixelDigi`s).


### RecHits

!!! todo

	TODO????

### Pixel Cluster

Each time particles pass through the Pixel Detector and interact with it, several 
adjascent Pixels are activated at the same time from each
particle. Those adjascent Pixels form **Clusters**. In other words, a **Cluster** 
is the trace that the particle leaves on the Pixel Detector.

<figure markdown>
  ![Stains on a shirt](img/stains.jpg){ width="300" }
  <figcaption>A cluster of pixels activated by a particle resembles a stain (Pixel Cluster) on a shirt (Pixel Detector)</figcaption>
</figure>

## Software

### CMSSW

A [huge repository](https://github.com/cms-sw/cmssw) of software related to CMS
data analysis, reconstruction, also containing various other tools.

Intimidating for beginners, rumors have it that even people that have been
developing with it for ages don't want to touch it, not even with a 10-foot pole.

Resembles a mythic monster, that keeps growing with the passage of time, thriving on
the keyboard strokes of innocent physicists.

<figure markdown>
  ![CMSSW's latest selfie](img/hydra.jpg){ width="800" }
  <figcaption>
  It's not that ugly, CMSSW was just having a bad hair day when this
  pic was taken
  </figcaption>
</figure>

??? quote "Hydra image source"

	[@velinov](https://www.deviantart.com/velinov/art/Hydra-monster-144496963)

### `cmsRun`, `cmsDriver`, `runTheMatrix` etc.

Proceed to the [Tools section](cmssw/tools.md) for more information.

### Configuration files

Most probably referring to 
[`cmsRun` configuration files](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookConfigFileIntro)

### Pixel Clusterizing

Once data from each pixel has been recorded,
we need to detect **Clusters** of adjascent/neigbouring pixels which
have an ADC value above a certain threshold. This process of detecting
**Clusters** is called **Clusterizing**.

This is implemented in both [CPU](cmssw/cpu/PixelThresholdClusterizer-overview.md)
and [GPU](cmssw/gpu/gpuClustering-overview.md).

### SoA/AoS

Alternative ways to store class variables in memory for **parallel computing**.

In usual class declarations, one might have a class attribute, e.g. `int num_books`. 
When an instance of this class is loaded into memory, `num_books` is most probably close
to other class attributes. Say we have thousands of such class instances and
want to run parallel code on them using only the `num_books` attribute
of each instance. This way, the computer will have to load to memory lots of data
which will stay unused. 

This will degrade performance at low-levels, e.g. cache lines will be filled
with data unrelated to the actual calculations, meaning lots of wasted cycles.

Such an approach is called **Array of Structures (AoS)**, and descrbes using
an array of structures/classes for storing data.

To alleviate the drawbacks of AoS, instead of thousands of class instances
stored as an array, the **Structure of Arrays (SoA)** approach can be used. This
approach suggests using only *one* class instance for all the data one wants to store.

This way, instead of having an `int num_books` attribute in the class, the class now
contains an `int* nums_books` array, which stores the data of **all instances**.

The data are now stored consecutively in RAM when loaded, meaning less memory overhead
and better parallel code performance.

A video explanation may be found [here](https://www.youtube.com/watch?v=ScvpoiTbMKc)
