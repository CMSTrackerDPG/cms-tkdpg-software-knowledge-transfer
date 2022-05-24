# certify

## Overview

This is a multi-purpose view[^1] which handles the following:

- Renders a __form__ to certify a combination of ^^run number & reconstruction type^^
- Allows the user to submit a complete certification form.

The user can land on this page from:

- The `/openruns/` page:
  - By selecting a run number and (optionally) a reconstruction type on the
  top form (``GET`` request).
  - By clicking a colored button in the results listed after searching
  for openruns (bottom form, ``GET`` request).
- The `/certify/` page:
  - By submitting the complete certification form (``POST`` Request)

[^1]: Probably shouldn't be doing so many things :weary:

## Inputs

- Run number (`int`, from URL, **required**)
- Reconstruction type (`str`, from URL, optional)
- Dataset name (`str`, from `GET` parameters, optional, only used
when clicking on colored boxes in `/openruns/`)

## Behavior

