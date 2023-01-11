# Debugging CMSSW

First, build CMSSW with the `-g` flag (see [tools](tools.md#extra-parameters)).

Then, assuming you already have a configuration file to run via `cmsRun`, execute:

```bash
gdb --args cmsRun <your configuration file>
```

GDB will start and you can then type:

```gdb
catch throw
run
```
