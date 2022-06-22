# Usage

## Prerequisites

You will need to run the following command to acquire a proxy to access the grid:

```bash
voms-proxy-init -voms cms -rfc
```

## Execution

### List GPU workflows

``` sh
runTheMatrix.py -n --what gpu
```

??? quote "Example output"

	```
	ignoring non-requested file relval_standard
	...
	
	found a total of  22  workflows:
	136.885502 RunHLTPhy2018D+HLTDR2_2018+RECODR2_2018reHLT_Patatrack_PixelOnlyGPU+HARVEST2018_pixelTrackingOnly 
	136.885512 RunHLTPhy2018D+HLTDR2_2018+RECODR2_2018reHLT_ECALOnlyGPU+HARVEST2018_ECALOnly 
	136.885522 RunHLTPhy2018D+HLTDR2_2018+RECODR2_2018reHLT_HCALOnlyGPU+HARVEST2018_HCALOnly 
	136.888502 RunJetHT2018D+HLTDR2_2018+RECODR2_2018reHLT_Patatrack_PixelOnlyGPU+HARVEST2018_pixelTrackingOnly 
	136.888512 RunJetHT2018D+HLTDR2_2018+RECODR2_2018reHLT_ECALOnlyGPU+HARVEST2018_ECALOnly 
	136.888522 RunJetHT2018D+HLTDR2_2018+RECODR2_2018reHLT_HCALOnlyGPU+HARVEST2018_HCALOnly 
	10824.502 2018_Patatrack_PixelOnlyGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.506 2018_Patatrack_PixelOnlyTripletsGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.512 2018_Patatrack_ECALOnlyGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.522 2018_Patatrack_HCALOnlyGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.592 2018_Patatrack_GPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10824.596 2018_Patatrack_TripletsGPU+TTbar_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10842.502 2018_Patatrack_PixelOnlyGPU+ZMM_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	10842.506 2018_Patatrack_PixelOnlyTripletsGPU+ZMM_13TeV_TuneCUETP8M1_GenSim+Digi+RecoFakeHLT+HARVESTFakeHLT 
	11634.502 2021_Patatrack_PixelOnlyGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.506 2021_Patatrack_PixelOnlyTripletsGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.512 2021_Patatrack_ECALOnlyGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.522 2021_Patatrack_HCALOnlyGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.592 2021_Patatrack_GPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11634.596 2021_Patatrack_TripletsGPU+TTbar_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11650.502 2021_Patatrack_PixelOnlyGPU+ZMM_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	11650.506 2021_Patatrack_PixelOnlyTripletsGPU+ZMM_14TeV_TuneCP5_GenSim+Digi+Reco+HARVEST 
	
	22 workflows with 4 steps
	```


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
CMSSW environment ([Instructions here](../software.md)).

#### GPU

```bash
runTheMatrix.py -l 136.885502 --what gpu
```

`-l` specifies the workflow to run.

`--what` specifies, among other things, the device to run this workflow on.
