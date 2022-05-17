# Overview

A single data analysis pipeline using `cmsRun` requires multiple steps, each one 
requiring data fetching, analysing, configuring and lot of other details that 
can take a lot of time to get familiar with in order to produce useful output.

For this reason the concept of __workflows__ has been introduced so that multiple
steps can be executed from a single program, using a single unique 
identifying number ("Workflow number").

The program responsible for executing workflows is a Python script available 
on LXPLUS named `runTheMatrix.py` (its source code can be found [here](https://github.com/cms-sw/cmssw/blob/master/Configuration/PyReleaseValidation/scripts/runTheMatrix.py)).
