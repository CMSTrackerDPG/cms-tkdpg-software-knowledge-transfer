# Overview
A `PixelDigi` represents an actual pixel of the Pixel Detector in software. It's a class
that encapsulates:

- The pixel's coordinates on the detector (row, column)
- The pixel's ADC value (i.e. the actual sensor value)
- 

It is located in `DataFormats/SiPixelDigi/interface`.

## Class attributes

### `theData` 

A single attribute of `PackedDigiType` which aims to contain all the required information in
the `PixelDigi` instance:

- `row`
- `col`
- `adc` value
- `flag`


## Class functions

### `row` (accessor)

The row that the Digi belongs to in the Pixel Detector.

### `column` (accessor)

The col that the Digi belongs to in the Pixel Detector.

### `flag` (accessor)

### `adc` (accessor)

### `packedData` (accessor)

