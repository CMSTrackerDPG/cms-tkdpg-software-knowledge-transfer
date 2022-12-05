# runTheMatrix Usage

## Prerequisites

You will need to run the following command to acquire a proxy to access the grid, if the workflow requires access to data on the grid:

```bash
voms-proxy-init -voms cms -rfc
```
Workflows that generate their own data during `step1` should not need a proxy.

## Execution

### List GPU workflows

``` sh
runTheMatrix.py -n --what gpu
```

??? quote "Example output"

	```
	ignoring non-requested file relval_standard
	...
	
	found a total of  58  workflows:
	136.885502 RunHLTPhy2018D+HLTDR2_2018+RECODR2_2018reHLT_Patatrack_PixelOnlyGPU+HARVEST2018_pixelTrackingOnly
	136.885512 RunHLTPhy2018D+HLTDR2_2018+RECODR2_2018reHLT_ECALOnlyGPU+HARVEST2018_ECALOnly 
	136.885522 RunHLTPhy2018D+HLTDR2_2018+RECODR2_2018reHLT_HCALOnlyGPU+HARVEST2018_HCALOnly 
	136.888502 RunJetHT2018D+HLTDR2_2018+RECODR2_2018reHLT_Patatrack_PixelOnlyGPU+HARVEST2018_pixelTrackingOnly 
	136.888512 RunJetHT2018D+HLTDR2_2018+RECODR2_2018reHLT_ECALOnlyGPU+HARVEST2018_ECALOnly 
	136.888522 RunJetHT2018D+HLTDR2_2018+RECODR2_2018reHLT_HCALOnlyGPU+HARVEST2018_HCALOnly 
	10824.502 2018_Patatrack_PixelOnlyGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.503 2018_Patatrack_PixelOnlyGPU_Validation+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.504 2018_Patatrack_PixelOnlyGPU_Profiling+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT 
	10824.506 2018_Patatrack_PixelOnlyTripletsGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.507 2018_Patatrack_PixelOnlyTripletsGPU_Validation+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.508 2018_Patatrack_PixelOnlyTripletsGPU_Profiling+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT 
	10824.512 2018_Patatrack_ECALOnlyGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.513 2018_Patatrack_ECALOnlyGPU_Validation+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.514 2018_Patatrack_ECALOnlyGPU_Profiling+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT 
	10824.522 2018_Patatrack_HCALOnlyGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.523 2018_Patatrack_HCALOnlyGPU_Validation+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.524 2018_Patatrack_HCALOnlyGPU_Profiling+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT 
	10824.582 2018_Patatrack_AllGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.583 2018_Patatrack_AllGPU_Validation+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.586 2018_Patatrack_AllTripletsGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.587 2018_Patatrack_AllTripletsGPU_Validation+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.592 2018_Patatrack_FullRecoGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.593 2018_Patatrack_FullRecoGPU_Validation+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.596 2018_Patatrack_FullRecoTripletsGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.597 2018_Patatrack_FullRecoTripletsGPU_Validation+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10842.502 2018_Patatrack_PixelOnlyGPU+ZMM_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10842.503 2018_Patatrack_PixelOnlyGPU_Validation+ZMM_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10842.504 2018_Patatrack_PixelOnlyGPU_Profiling+ZMM_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT 
	10842.506 2018_Patatrack_PixelOnlyTripletsGPU+ZMM_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT
	10842.507 2018_Patatrack_PixelOnlyTripletsGPU_Validation+ZMM_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10842.508 2018_Patatrack_PixelOnlyTripletsGPU_Profiling+ZMM_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT 
	11634.502 2021_Patatrack_PixelOnlyGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.503 2021_Patatrack_PixelOnlyGPU_Validation+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.504 2021_Patatrack_PixelOnlyGPU_Profiling+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano 
	11634.506 2021_Patatrack_PixelOnlyTripletsGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.507 2021_Patatrack_PixelOnlyTripletsGPU_Validation+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.508 2021_Patatrack_PixelOnlyTripletsGPU_Profiling+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano 
	11634.512 2021_Patatrack_ECALOnlyGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.513 2021_Patatrack_ECALOnlyGPU_Validation+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.514 2021_Patatrack_ECALOnlyGPU_Profiling+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano 
	11634.522 2021_Patatrack_HCALOnlyGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.523 2021_Patatrack_HCALOnlyGPU_Validation+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.524 2021_Patatrack_HCALOnlyGPU_Profiling+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano 
	11634.582 2021_Patatrack_AllGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.583 2021_Patatrack_AllGPU_Validation+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.586 2021_Patatrack_AllTripletsGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.587 2021_Patatrack_AllTripletsGPU_Validation+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.592 2021_Patatrack_FullRecoGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.593 2021_Patatrack_FullRecoGPU_Validation+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.596 2021_Patatrack_FullRecoTripletsGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11634.597 2021_Patatrack_FullRecoTripletsGPU_Validation+TTbar_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11650.502 2021_Patatrack_PixelOnlyGPU+ZMM_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11650.503 2021_Patatrack_PixelOnlyGPU_Validation+ZMM_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11650.504 2021_Patatrack_PixelOnlyGPU_Profiling+ZMM_14TeV_TuneCP5_GenSim+Digi+RecoNano 
	11650.506 2021_Patatrack_PixelOnlyTripletsGPU+ZMM_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano 
	11650.507 2021_Patatrack_PixelOnlyTripletsGPU_Validation+ZMM_14TeV_TuneCP5_GenSim+Digi+RecoNano+HARVESTNano
	11650.508 2021_Patatrack_PixelOnlyTripletsGPU_Profiling+ZMM_14TeV_TuneCP5_GenSim+Digi+RecoNano 
	
	12 workflows with 3 steps
	46 workflows with 4 steps


	```

!!! note

	* Workflows starting with `136.885` or `136.888` contain actual, detector data.
	* Workflows starting with `11634` are Monte Carlo (simulated) data.
	* Workflows ending in `.504` are usually used for profiling, as they don't run any DQM code.
	* Workflows ending in `.508` detect Triplets (tracks using only 3 hits, instead of 4) and
	are also used for profiling.
	* More information on specific workflows [here](https://patatrack.web.cern.ch/patatrack/wiki/workflows/)

### List Profiling workflows

Some of the workflows mentioned here, for example the [profiling ones](history.md#4-add-workflows-for-profiling-the-gpu-code-35540), can be found by running:

```sh
runTheMatrix.py -n --what upgrade | grep Patatrack
```

??? quote "Example output"

	```
	...
	11442.595 2018DesignPU_Patatrack_TripletsCPU+ZMM_13TeV_TuneCUETP8M1_GenSim+DigiPU+RecoFakeHLTPU+HARVESTFakeHLTPU 
	11442.596 2018DesignPU_Patatrack_TripletsGPU+ZMM_13TeV_TuneCUETP8M1_GenSim+DigiPU+RecoFakeHLTPU+HARVESTFakeHLTPU 
	11634.501 2021_Patatrack_PixelOnlyCPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.502 2021_Patatrack_PixelOnlyGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.504 2021_Patatrack_PixelOnlyGPU_Profiling+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco 
	11634.505 2021_Patatrack_PixelOnlyTripletsCPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.506 2021_Patatrack_PixelOnlyTripletsGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.508 2021_Patatrack_PixelOnlyTripletsGPU_Profiling+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco 
	11634.511 2021_Patatrack_ECALOnlyCPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.512 2021_Patatrack_ECALOnlyGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.514 2021_Patatrack_ECALOnlyGPU_Profiling+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco 
	11634.521 2021_Patatrack_HCALOnlyCPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.522 2021_Patatrack_HCALOnlyGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.524 2021_Patatrack_HCALOnlyGPU_Profiling+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco 
	11634.591 2021_Patatrack_CPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.592 2021_Patatrack_GPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.595 2021_Patatrack_TripletsCPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.596 2021_Patatrack_TripletsGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11650.501 2021_Patatrack_PixelOnlyCPU+ZMM_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11650.502 2021_Patatrack_PixelOnlyGPU+ZMM_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11650.504 2021_Patatrack_PixelOnlyGPU_Profiling+ZMM_14TeV_TuneCP5_GenSim+Digi+Reco 
	...
	```

### Running a workflow

Before you run a workflow, you will first need to activate a
CMSSW environment ([Instructions here](../working-with-cmssw/setup.md)).

#### GPU

Login into any GPU-enabled machine and, from within an activated CMS environment
run any GPU workflow, e.g:

```bash
runTheMatrix.py -l 136.885502 --what gpu
```

`-l` specifies the workflow to run.

`--what` specifies, among other things, the device to run this workflow on. 

#### CPU

See [notes](#notes).

### Notes

1. `--what upgrade` {==TODO==} 
2. Some workflows switche to CPU or GPU automatically, depending on the system's capabilities, e.g.:

```bash
runTheMatrix.py -w upgrade -l 11634.502
```

### Expected outputs

`runTheMatrix.py` executes `cmsDriver` for each step. A directory named
after the complete name of the workflow will be created on each workflow execution.
In it, logs and intermediate files are created. 

To debug errors for a specific step, look into those log files.

### Possible Errors

#### No `.root` files are generated during Step 2

##### Possible Solution

Use the `--ibeos` argument along with `runTheMatrix.py`

