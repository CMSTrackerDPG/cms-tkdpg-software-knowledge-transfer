# Basic concepts

Before diving into the code, some terms and concepts must be covered
so that the reader does not get lost in the abundance of terminology
and jargon that pervades anything CERN-related.

Your companion glossary can always be found
[here](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookGlossary).

!!! warning

	There might be lots of inaccuracies in this document. Don't judge me!

## Pixel Detector

!!! todo
	
	TODO
	
[Comprised of many Pixels](https://cms.cern/detector/identifying-tracks/silicon-pixels), 
the Pixel Detector is part of the whole CMS detector.


In the reconstruction code, the Pixel Detector is represented as a 2D matrix of
pixels (`PixelDigi`s).

## PixelDigi

!!! todo
	
	TODO

A `PixelDigi` represents an actual pixel of the Pixel Detector in software. It's a class
that encapsulates:

- The pixel's coordinates on the detector (row, column)
- The pixel's ADC value (i.e. the actual sensor value)
- 

## RecHits

!!! todo

	TODO????

## Pixel Cluster

Each time particles pass through the Pixel Detector and interact with it, several 
adjascent Pixels are activated at the same time from each
particle. Those adjascent Pixels form **Clusters**. In other words, a **Cluster** 
is the trace that the particle leaves on the Pixel Detector.

<figure markdown>
  ![Stains on a shirt](img/stains.jpg){ width="300" }
  <figcaption>A cluster of pixels activated by a particle resembles stains on a shirt</figcaption>
</figure>

## Pixel Clusterizing

Once each pixel has been recorded and converted into a `PixelDigi`,
we need to detect **Clusters** of adjascent/neigbouring digis which
have an ADC value above a certain threshold. This process of detecting
**Clusters** is called **Clusterizing**.

