# Introduction

!!! todo

	TODO
	
![MLP logo](img/mlp_logo.png){ width="500pt" }

## Purpose

This is a [Django](../general/django/overview.md) project aimed at:

- Managing the storage of CMS data:
	- Run and Lumisection Information.
	- Run and Lumisection Histograms.
	- Run and Lumisection Certification.
- Providing an API for serving this data to any user that requires it.
	- Storing datasets used by users to train algorithms.
	- Storing parameters and results of said algorithms.
   
## Deployment on Openshift

The app is deployed [here](https://ml4dqm-playground.web.cern.ch/).
For more information on its deployment, go [here](deploying/deployments.md).

## Deploying locally

Basic instructions for Django projects can be found [here](../general/django/setup/overview.md).

## Composition

The project is comprised of the Data Engineering part (DE) and
the Data Science part (DS). Each one has its own repository:

* [Data Engineering repository](https://github.com/cmstrackerdpg/mlplayground)
* [Data Science repository](https://github.com/XavierAtCERN/dqm-playground-ds/)


