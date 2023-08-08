# OMS Models

Information on the models described in the `oms/models.py` file.

## `OmsRun`

Contains information on CMS Runs. Not to be confused with
[LHC Runs](../../../basic-concepts.md#lhc-run-1-run-2).

### Fields

#### `apv_mode`

!!! warning
	
	No documentation was found for what APV mode is
	
A field with possible values `DECO` or `PEAK`. Displayed
as a column in the list of reference runs.
	
This information is acquired as a separate HTTP request to
[ebutz.web.cern.ch](http://ebutz.web.cern.ch). Updated when
model instance's [`update_apv_mode` method](#update_apv_mode) is called.

### Methods

#### `save`

The default `save` method is overridden so that specific flags
of the model instance are updated:

* `run_type`

#### `update_apv_mode`

Private method to fetch APV mode information from ebutz.web.cern.ch.
Updates the instance's `apv_mode` field and saves the model instance.
