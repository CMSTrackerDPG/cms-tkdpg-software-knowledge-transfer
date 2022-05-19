# PixelDigi overview

A `PixelDigi` represents an actual pixel of the Pixel Detector in software. It's a class
that encapsulates:

- The pixel's coordinates on the detector (row, column)
- The pixel's ADC value (i.e. the actual sensor value)
- The flag ???? {==TODO==}

and contains all this information in a packed format.

It is located in `DataFormats/SiPixelDigi/interface`.

![UML diagram of the `PixelDigi` class](img/uml_PixelDigi.png)

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

The row that the Digi belongs to in the Pixel Detector. Extracted from the `theData`.

### `column` (accessor)

The col that the Digi belongs to in the Pixel Detector. Extracted from the `theData`.

### `flag` (accessor)

Extracts `flag` from the `theData`.

### `adc` (accessor)

Extracts `adc` from the `theData`.

### `packedData` (accessor)

Returns the `theData` attribute.
