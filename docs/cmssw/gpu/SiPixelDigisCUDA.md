# SiPixelDigisCUDA.h

Class used to contain pixel data (coordinates, ADC values) using the SoA approach,
intended to be used by CUDA code.

The actual data is stored in an instance of
[SiPixelDigisCUDASOAView](SiPixelDigisCUDASOAView.md), which
is accessed by the class' `m_view` attribute, via the `view()`
method.

File on [github](https://github.com/cms-sw/cmssw/blob/master/CUDADataFormats/SiPixelDigi/interface/SiPixelDigisCUDA.h)

## UML diagram

```mermaid
classDiagram
class SiPixelDigisCUDA{
	-SiPixelDigisCUDASOAView m_view
	-m_store
	+view() SiPixelDigisCUDASOAView
}

class SiPixelDigisCUDASOAView{
		-uint16_t* xx_
		-uint16_t* yy_		
		-uint16_t* adc_		
		-uint16_t* moduleInd_		
		-int32_t* clus_				
		-uint32_t* pdigi_
		-uint32_t* rawIdArr_		
		
		+xx() uint16_t*
		+yy() uint16_t*		
		+adc() uint16_t*		
		+moduleInd() uint16_t*		
		+clus() int32_t*		
		+pdigi() uint32_t*				
		+rawIdArr() uint32_t*						
}

SiPixelDigisCUDA "*" --* "1" SiPixelDigisCUDASOAView : m_view
```

## Methods

### `view`

Accesses the data stored in the `m_view` attribute, which are
stored with the [SoA](../../basic-concepts.md#soaaos) approach.

