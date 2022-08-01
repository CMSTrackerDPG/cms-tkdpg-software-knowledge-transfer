# ShiftLeader Report Presentation

The `ShiftLeaderReportPresentationView` calls the `ShiftLeaderReportPresentation`
class (in `shiftleader/utiltities/shiftleader_report_presentation.py`) which 
creates an Open Document Format Presentation using [`odfpy`](https://github.com/eea/odfpy). 

## Notes & Tips

!!! warning
	
	The OpenDocumentFormat can be *very* confusing at first.
	You are advised to follow some simple examples found in the 
	[`odfpy`](https://github.com/eea/odfpy/blob/master/api-for-odfpy.odt)
	repository. 
	Based on those, [these prototypes](https://github.com/nothingface0/odfpy-test) 
	were created to test different setups and combinations.
	
!!! danger ""

	`Style`s suck
	
!!! tip "File format"

	OpenDocumentFormat files are actually archives, containing two very
	important files:
	
	- `styles.xml`
	- `content.xml`
	
!!! tip "Styles"

	Some styles must have specific names, while `automaticstyles` 
	do not have this requirement. 
	
!!! tip "Automatic styles location"

	`automaticstyles` are stored in `content.xml`
	
!!! tip "Making an ODF file programmatically"

	You are generally advised to make changes to an existing `.odp`/`.odt` file and
	then open them as archives to check the `contents.xml` and `styles.xml` files
	to see how those are structured, instead of trying to understand the vast
	specification from scratch.
	

## Links

- [OASIS Open Document Format 1.2 Specification](http://docs.oasis-open.org/office/v1.2/os/OpenDocument-v1.2-os-part1.pdf#__RefHeading__1416402_253892949)
- [`odfpy`](https://github.com/eea/odfpy) (Contains examples and reference, albeit not so good) 
