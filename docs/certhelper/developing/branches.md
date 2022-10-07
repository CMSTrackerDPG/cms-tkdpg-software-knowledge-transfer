# CertHelper Branches

!!! todo
	
	TODO
	
	
## Overview

There are three main branches in the repository, each with its own
importance:

- `master` branch, which contains code which is 
  {==thoroughly tested==} and production-ready, meaning
  as few bugs as possible (if not zero). CertHelper's production instance
  builds from this branch.
- `training` branch, which should be synchronized with the `master` branch
  at all times. It contains minor changes which make it useful for training
  new Shifters. CertHelper's training instance builds from this branch.
- `develop` branch, where all development takes place. CertHelper's development
  instance builds from this branch.
  
## Developing

The following is a suggested strategy for working with CertHelper branches.

### Working on a feature or bug

- Create an issue on [github](https://github.com/CMSTrackerDPG/certifier/issues)
- Create a new branch with the number of the issue as a name, starting from `develop`, i.e.:
  ```bash
  git checkout develop
  git pull origin develop
  git checkout -b "#71"
  ```
  and do whatever changes you need to do.
- Push the branch to github:
  ```bash
  git push origin "#71"
  ```
- Go to github and create a Pull Request to merge the `#71` branch to the `develop` one (important! Don't choose the `master` as target!)
- Merge your Pull Request to `develop`.

You can start new builds on the
[`certhelper-dev`](https://paas.cern.ch/topology/ns/certhelper-dev?view=graph)
deployment at will, which will pull all latest changes from the `develop` branch.

### Merging `develop` to `master`

Once you have tested your changes on the development deployment, you will
need to update the main CertHelper instance.

- Make sure you have [updated the `CERTHELPER_VERSION`](../../general/development/versioning.md)
string in `dqmhelper/settings.py`, following the [Semantic Versioning](https://semver.org/)
guide.
- Merge the `develop` branch to `master`:
  ```bash
  git checkout master
  git pull origin master
  git merge --no-ff develop
  git push master
  ```
- Put a tag on the `master` branch branch, with the same name as the `CERTHELPER_VERSION` string, e.g.:
  ```bash
  git checkout master
  git tag 1.10.0
  git push --tags
  ```
- Merge the `master` branch to `training`:
  ```bash
  git checkout training
  git pull origin training
  git merge master
  git push training
  ```


  
!!! warning
	
	The `master` and `training` branches must always be in sync.
