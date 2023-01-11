# Debugging CMSSW

First, build CMSSW with the `-O0 -g` flags (see [tools](tools.md#extra-parameters)).

Then, assuming you already have a configuration file to run via `cmsRun`,
follow the instructions [here](https://twiki.cern.ch/twiki/bin/view/Main/HomerWolfeCMSSWAndGDB).

## Quick reference

### Set Breakpoint in file line

```gdb
break '/absolute/path/to/file.c':LINE
```

You should see a response like this:

```
No source file named /data/user/dpapagia/cmssw/CMSSW_13_0_X_2023-01-09-1100/src/RecoPixelVertexing/PixelTriplets/plugins/CAHitNtupletGeneratorOnGPU.cc.
Make breakpoint pending on future shared library load? (y or [n]) y
Breakpoint 3 ('/data/user/dpapagia/cmssw/CMSSW_13_0_X_2023-01-09-1100/src/RecoPixelVertexing/PixelTriplets/plugins/CAHitNtupletGeneratorOnGPU.cc':293) pending.
```
