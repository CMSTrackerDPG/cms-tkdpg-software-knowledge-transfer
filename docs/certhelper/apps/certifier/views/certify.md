# CertifyView

## Overview

This is a multi-purpose[^1] class-based view which handles the following:

- Renders a __form__ to certify a combination of ^^run number & reconstruction type^^
- Allows the user to submit a complete certification form.

The final purpose is to guide the user create `TrackerCertification` objects.

The user can land on this page from:

- The `/openruns/` page:
  - By selecting a run number and (optionally) a reconstruction type on the
  top form (``GET`` request).
  - By clicking a colored button in the results listed after searching
  for openruns (bottom form, ``GET`` request).
- The `/certify/` page:
  - By submitting the complete certification form (``POST`` Request)

[^1]: And messy too :weary:

## Inputs

- Run number (`int`, from URL, __required__)
- Reconstruction type (`str`, from URL, _optional_)
- Dataset name (`str`, from `GET` parameters, _optional_, only used
when clicking on colored boxes in `/openruns/`)

## Behavior

### Overview 

#### On class creation

This is common behavior for both `GET` and `POST`.

- Make sure that a run number and a reconstruction type are specified.
- Make sure that current user is allowed to certify specific reconstruction.
- If certification exists and user is the owner, redirect to update it, else
continue below.
- Make sure an `OmsRun` object exists for specific run number.

#### `GET`

- Create a form for the user to certify the reconstruction. This
form also contains information about whether there was complete information
from RunRegistry __and__ OMS API at the time (`external_info_completeness`). 

#### `POST`

- Get or create a `RunReconstruction` object given the run number and the
reconstruction type.
- If the dataset is specified (e.g. `/Express/Commissioning2022/DQM`), 
`get_or_create` a `Dataset` object.
- Parse the `POST`ed form.
- Check whether a `TrackerCertification` object exists for this
combination of parameters, else create it.

### If only a run number is supplied

This case is valid if the user navigates to `/openruns/` and
only specifies a run number before pressing `Certify`:

![](img/run_number_specified.png)

The procedure is as follows:

- Try querying the RunRegistry using the supplied run number
to get the next available __uncertified dataset__
(e.g. `/Express/Commissioning2022/DQM`). This is done using
the `rr_retrieve_next_uncertified_dataset` function.
- Then, the __reconstruction type__ is specified, using the dataset
name acquired in the previous step, using the `get_reco_from_dataset`
function (which simply searches for specific keywords in the dataset
string, e.g. in the previous example, the reconstruction type would 
be `express`).

This procedure could raise:

- `RunReconstructionAllDatasetsCertified` if no uncertified datasets
are found for this run number in RunRegistry. This redirects back
to the `/openruns/` page, so that the user can choose another run number.
- `ConnectionError` if RunRegistry is not accessible, and `ParseError`
if there's some CERN SSO outage
(see issue [#136](https://github.com/CMSTrackerDPG/certifier/issues/136)).
This is pretty much equivalent with `ConnectionError`. In this case, the
user is not allowed to proceed, since there is not enough information. A
reconstruction type should be also supplied.

### If a combination of run_number and reconstruction type is specified

The procedure is:

- The dataset is retrieved from RunRegistry using the run number and the
reconstruction type specified (`rr_retrieve_dataset_by_reco`).

### If a dataset is specified but not a reconstruction type

This case applies when the user clicks any of the dataset buttons on
the `/openruns/` page, in the table generated when searching for
open runs.
