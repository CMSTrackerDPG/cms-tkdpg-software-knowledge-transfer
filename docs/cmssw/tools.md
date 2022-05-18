# Tools

It all starts with `cmsRun` which is the main entry point to all CMSSW code.
Configuring it can be a pain so, for this reason, `cmsDriver` was built.
Still, configuring `cmsDriver` can also be tedious for pipelines that require
multiple steps of data fetching, configuring, simulating etc., so `runTheMatrix.py`
was developed.

Those tools are available on LXPLUS machines, after creating a work area
and activating a CMSSW environment by `cmsenv`.

## `cmsRun`

!!! todo
	
	TODO

## `cmsDriver`

!!! todo
	
	TODO

## `runTheMatrix.py`

A wrapper script which is responsible to execute multi-step analysis/simulation recipes
by configuring `cmsDriver`.

It is examined in more detail [here](working-with-cmssw/workflows/overview.md)
