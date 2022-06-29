# Deployment

The project is deployed on CERN's
[PaaS platform](https://paas.cern.ch/topology/ns/ml4dqm-playground?view=graph)
as two separate deployments:

* `ml-playground` using S2i deployment,
* `dqm-playground-ds` from a Docker image.

## `dqm-playground-ds`

This project is automatically built into a Docker image using GitHub Actions
and published on [Docker Hub](https://hub.docker.com/r/xavier2c/dqm-playground-ds).

On each new image build, an update is rolled out and the deployment
is updated.

This was configured using
[these instructions](https://paas.docs.cern.ch/2._Deploy_Applications/Deploy_Docker_Image/2-automatic-redeployments/).
