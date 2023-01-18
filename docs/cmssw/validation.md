# Validation

This section will describe the basic steps one needs to do in order
to validate that their code, apart from syntactically correct, also
produces correct results.

This usually boils down to:

- Executing specific [workflows](workflows/overview.md). The specific workflow to execute
depends on the code you changed. See [Workflow numbers](#workflow-numbers-for-validation) below.
- Comparing the results from an execution of a unchanged CMSSW version.

## Procedure

- Checkout to an unchanged CMSSW version you want to compare results against.
- [Build](build.md) it.
- [Execute](workflows/usage.md) the appropriate workflow.
- Create the validation plots:
```bash
harvestTrackValidationPlots.py step3_inDQM*.root -o file_base.root
```
The `file_base.root` which will be produced is going to be used later.
- [Execute](workflows/usage.md) the appropriate workflow after building your code.
- Checkout to the code where you've made your changes.
- `scram b clean` and [re-build](build.md) the code.
- [Execute](workflows/usage.md) the **same** appropriate workflow.
- Create the validation plots:
```bash
harvestTrackValidationPlots.py step3_inDQM*.root -o file_changes.root
```
- Make the validation plots:
```bash
makeTrackValidationPlots.py file_base.root file_changes.root
```

## Workflow numbers for validation

Workflows to execute for validation.

### PixelTrack

- 11634.502
- 11834.502
- 11604.0
- 11609.0
