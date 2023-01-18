# Building the code

!!! info
	
	See also [scram](tools.md#scram).


## Normal build

```bash
scram b -j `nproc`
```

## Building for debugging

Whether you prefer debugging with extra verbose messages or by [using `gdb`](debugging.md),
those options will help you add extra parameters to `scram`:

- For enabling `LogDebug` messages, run `export USER_CXXFLAGS="-DEDM_ML_DEBUG"` before runningthe `scram` command.
- For also defining the `GPU_DEBUG` flag globally (for GPU code), run
`export USER_CXXFLAGS="-O0 -g -DGPU_DEBUG -DEDM_ML_DEBUG"` before running the `scram` command.
- If your debug build is not working, you might need to clean your development area:
``` bash
scram b clean
```

## Rebuilding

If you built CMSSW, changed a file and rebuilt it, some cached object files may still
be there. Usually, this should not be a problem, and you should only need to re-run `scram b`
after changing any file.

If you want to be 100% certain that no cached files are used, run `scram b clean` before re-running `scram b`.

## Multiple jobs

Note that running `scram b` with multiple jobs launches multiple threads on multiple source
files. The compilation order will not be predictable, and the complilation messages
will not be predictable.
