# Developing for CMSSW

## Cloning a CMSSW release on your LXPLUS machine

### Before you start

Please make sure you registered to GitHub and that you have provided them 
a ssh public key to access your private repository.

### Connect to your LXPLUS machine

!!! todo

	TODO

### Search for available releases

Search for available releases:

```bash
scram list CMSSW
```

This should result in some output like:

```
Listing installed projects available for platform >> slc7_amd64_gcc10 <<

--------------------------------------------------------------------------------
| Project Name  | Project Version          | Project Location                  |
--------------------------------------------------------------------------------

  CMSSW           CMSSW_12_0_0_pre3          
                                         --> /cvmfs/cms.cern.ch/slc7_amd64_gcc10/cms/cmssw/CMSSW_12_0_0_pre3
  CMSSW           CMSSW_12_0_0_pre4          
                                         --> /cvmfs/cms.cern.ch/slc7_amd64_gcc10/cms/cmssw/CMSSW_12_0_0_pre4
  CMSSW           CMSSW_12_0_0_pre5          
                                         --> /cvmfs/cms.cern.ch/slc7_amd64_gcc10/cms/cmssw/CMSSW_12_0_0_pre5
...
  CMSSW           CMSSW_12_3_DBG_X_2022-02-10-2300  
                                         --> /cvmfs/cms-ib.cern.ch/week1/slc7_amd64_gcc10/cms/cmssw/CMSSW_12_3_DBG_X_2022-02-10-2300
  CMSSW           CMSSW_12_3_SKYLAKEAVX512_X_2022-02-10-2300  
                                         --> /cvmfs/cms-ib.cern.ch/week1/slc7_amd64_gcc10/cms/cmssw/CMSSW_12_3_SKYLAKEAVX512_X_2022-02-10-2300
  CMSSW           CMSSW_12_3_X_2022-02-11-1100  
                                         --> /cvmfs/cms-ib.cern.ch/week1/slc7_amd64_gcc10/cms/cmssw-patch/CMSSW_12_3_X_2022-02-11-1100
```

### Create a CMSSW area

Once you have decided on a CMSSW release, set up the work area:

``` bash
cmsrel CMSSW_12_3_X_2022-02-11-1100
cd CMSSW_12_3_X_2022-02-11-1100/src
cmsenv
```

These commands clone a specific release of CMSSW, then activate the CMSSW environment,
where the paths of most of the CMSSW tools are made available.

### init git area

``` bash
git cms-init --upstream-only
```

### Checkout a few packages using git cms-addpkg

Some useful packages for developing the *pixel local reconstruction in CUDA*:

```
CUDADataFormats/SiPixelCluster/
CUDADataFormats/SiPixelDigi/
RecoLocalTracker/SiPixelClusterizer/
```

You can add these packages with **cms-addpkg**:

(You need to be in the `CMSSW_12_3_X_2022-02-11-1100/src` repository)

``` bash
git cms-addpkg CUDADataFormats/SiPixelCluster/
git cms-addpkg CUDADataFormats/SiPixelDigi/
git cms-addpkg RecoLocalTracker/SiPixelClusterizer/
```

See the checked out packages in `.git/info/sparse-checkout`

??? quote "Output of `cat .git/info/sparse-checkout`"

	``` bash
	/.clang-format
	/.clang-tidy
	/.gitignore
	/CUDADataFormats/SiPixelCluster/
	/CUDADataFormats/SiPixelDigi/
	/RecoLocalTracker/SiPixelClusterizer/
	```

Only build the desired packages, **add/remove** unwanted ones:  
[http://cms-sw.github.io/git-cms-addpkg.html](http://cms-sw.github.io/git-cms-addpkg.html)

**Fetch updates from remote**
```sh
git checkout master
git fetch [official-cmssw]
git merge official-cmssw/master master
```

Where *official-cmssw* is the remote name configured for [the CMSSW offline software repository](https://github.com/cms-sw/cmssw).

You can list your remote repositories with (for even more verbose output, you can stack the v-s):
```sh
git remote -v
```



## Tutorial: proposing changes to CMSSW

**Based on [http://cms-sw.github.io/tutorial.html](http://cms-sw.github.io/tutorial.html)**


**Conflict after PR**  
To resolve a conflict that appeared after proposing the changes in a PR, one should prefer **rebase** to **merge** as it keeps the commit history clean.

For a rebase, do:

```sh
git checkout <development_branch_name>
git fetch
git rebase -i <CMSSW_current_release>
```

It might be, that the tags for branches are only considered for your **default remote**, eg. my-cmssw, but the current release of CMSSW is not included in that. To resolve this, you can also try specifying the remote for the **official cmssw** (which is not a fork):

```sh
git rebase -i <official_cmssw_name>/<CMSSW_current_release>
```

### Build release

``` bash
scram b -j
```

Doing a `DEBUG` build for `GPU` development:

``` bash
USER_CXXFLAGS="-g -DGPU_DEBUG -DEDM_ML_DEBUG" scram b -j
```

If your debug build is not working, you might need to clean your development area:
``` bash
scram b clean
```

### Before making a PR

``` bash
scram b code-format # run clang-format
scram b code-checks # run clang-tidy
```

