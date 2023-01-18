# CMSSW FAQ

## CMSSW Tools

### `fatal: unable to find remote helper for 'https'` errors

If you keep getting `fatal: unable to find remote helper for 'https'` errors
when running `git` commands, it's most probably due to an unsuccessful `cmsenv`
execution (e.g. running `cmsenv` in an more than two weeks old `CMSSW_*_*_X`version). 
Just log out and login again.

### Debugging build tools (such as `edmWriteConfigs`)

Tools used during `scram b`, such as
[`edmWriteConfigs`](https://github.com/cms-sw/cmssw/blob/master/FWCore/ParameterSet/bin/edmWriteConfigs.cpp),
can also be edited by the user and can, therefore, be debugged. 

If the source code of the tool has not been checked out by the user, a precompiled one will be used.
If the user has checked out the package containing the tool and builds CMSSW, the newly compiled
tool will be used instead.

For example, if `edmWriteConfigs` hangs during `scram b` (resulting in `scram b` never finishing, with
the message `@@@@ Running edmWriteConfigs for RecoPixelVertexingPixelTripletsPlugins` being the last one
printed), you could:

- `git cms-addpkg FWCore/ParameterSet`
- Edit the `FWCore/ParameterSet/bin/edmWriteConfigs.cpp` file to print stuff you want to check.
- `scram b` as usual.
