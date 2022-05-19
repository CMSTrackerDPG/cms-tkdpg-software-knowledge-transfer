# copy_to_buffer


A class function that does *more* that its name suggests.

## Algorithm
- Given a linked list of `DigiIterator`s:
  - For each `DigiIterator` it calibrates the ADC value to electron counts.
  - For each `DigiIterator`:
	- Checks if its `adc` value is above `thePixelThreshold` and, if it is:
		- Updates the class attribute `theBuffer` (the actual Pixel matrix) 
		with the pixels ADC value.
		- If the `adc` value is also above `theSeedThreshold`, the position 
		(row & col) of the `DigiIterator` is stored in `theSeeds` array.
		- If this specific Digi has been recorded more than once (i.e. the row/col
		combination has already been encountered in a previous `DigiIterator`), 
		the Digi is removed from `theSeeds` array and the ADC value of the corresponding 
		place (coordinates) is set to 0.
	
