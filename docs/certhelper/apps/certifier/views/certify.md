# certify

This is a multi-purpose view[^1] which handles the following:

- Displays a form to certify a combination of run number & reconstruction type
- Allows the user to submit a complete certification form.

The user can land on this page from:

- The `/openruns/` page:
  - By selecting a run number and (optionally) a reconstruction type on the
  top form (``GET`` request).
  - By clicking a colored button in the results listed after searching
  for openruns (bottom form, ``GET`` request).
- The `/certify/` page:
  - By submitting the complete certification form (``POST`` Request)

[^1]: Probably shouldn't
