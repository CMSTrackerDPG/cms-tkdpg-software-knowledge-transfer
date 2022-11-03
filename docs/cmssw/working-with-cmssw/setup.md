# Setup and Usage

## Overview

The usual way to work with CMSSW is the following (from within [one of the available machines of your choice](#connect-to-your-machine)):

- [Clone](#create-a-cmssw-area) one of the available versions of CMSSW.
- [Checkout packages](#checkout-a-few-packages-using-git-cms-addpkg)
that you want to make changes to.
- Make changes to any file you want.
- Build CMSSW again.
- Execute [Workflows](workflows/overview.md) and validation.

To do so, it is recommended that you work with CMSSW on an [appropriately configured machine](#connect-to-your-machine), where many 
useful tools for managing, code-checking, code-formatting, building are available.

## Connect to your machine

As an overview, you have three choices in order to run GPU-enabled code:

- The very heavily-used [`lxplus-gpu`](#lxplus-gpu) machines (**[NOT RECOMMENDED]**).
- The Point5's [Online Machines](#cms-point-5-machines) ({==RECOMMENDED==}).
- [Special nodes](#special-gpu-nodes).

### lxplus-gpu

The lxplus service offers `lxplus-gpu.cern.ch` for shared GPU instances - with limited isolation and performance.

One can connect similary as would do to the `lxplus.cern.ch` host domain.

    ssh <username>@lxplus-gpu.cern.ch [-X]
	
??? info "If connecting directly from your computer"
	
	You might need to initialize kerberos and execute bash again
	from within the machine:

	```bash
	kinit
	exec bash
	```

### CMS Point 5 Machines

??? info

	This section is taken from the 
	[CMS TWiki TriggerDevelopmentWithGPUs](https://twiki.cern.ch/twiki/bin/viewauth/CMS/TriggerDevelopmentWithGPUs) 
	page.

There are 10 machines available for general development and validation of the online reconstruction on GPUs:

* **`gpu-c2a02-35-01.cms`**
* **`gpu-c2a02-35-02.cms`**
* **`gpu-c2a02-37-01.cms`**
* **`gpu-c2a02-37-02.cms`** (currently without a GPU)
* **`gpu-c2a02-37-03.cms`**
* **`gpu-c2a02-37-04.cms`**
* **`gpu-c2a02-39-01.cms`**
* **`gpu-c2a02-39-02.cms`** ({==Preferred==})
* **`gpu-c2a02-39-03.cms`**
* **`gpu-c2a02-39-04.cms`**

These are dedicated machines for the development of the online reconstruction.

To access them, you will first need a **CMS online account**. See below for instructions.

#### Request a CMS Online account

To request access, please subscribe to the [cms-hlt-gpu](https://e-groups.cern.ch/e-groups/Egroup.do?egroupId=10346110&searchField=0&searchMethod=0&searchValue=cms-hlt-gpu&pageSize=30&hideSearchFields=false&searchMemberOnly=false&searchAdminOnly=false) e-group and send an email to [andrea.bocci@cern.ch](mailto:andrea.bocci@cern.ch), indicating:

* whether you already have an online account;
* your online or lxplus username;
* your full name and email.

#### How to connect

Requirements:

* Have a CMS online account and
* Be in the **`gpudev`** group.

To connect directly from your computer:

* Create a proxy:
  ```bash
  ssh -f -N -D18080 <username>@cmsusr.cern.ch
  ```
* Connect via SSH:
  ```bash
  ssh -o ProxyCommand='nc --proxy localhost:18080 --proxy-type socks5 %h %p' <username>@gpu-c2a02-39-02.cms
  ```
  or 
  ```bash
  ssh -o ProxyCommand='nc -x localhost:18080 -X 5 %h %p' <username>@gpu-c2a02-39-02.cms
  ```

!!! note
	
	More detailed instructions
	[here](https://twiki.cern.ch/twiki/bin/viewauth/CMS/TriggerDevelopmentWithGPUs#Connecting_to_the_machines)

#### Special configuration required

* To make commands like `cmsenv` and `cmsrel` available, run 
  ```bash
  source /cvmfs/cms.cern.ch/cmsset_default.sh
  ```
  first.
* To allow connecting to GitHub via HTTP:
    * [Configure the SOCKS proxy](https://twiki.cern.ch/twiki/bin/viewauth/CMS/TriggerDevelopmentWithGPUs#Configure_the_online_machines_fo)
	* Open the proxy:
	```bash 
	ssh -f -N cmsusr.cms
	```
	* Configure `git`:
	```bash
	git config --global --replace-all http.proxy socks5://localhost:18080
	```
* Set the correct `SCRAM_ARCH` for these machines:
```bash
export SCRAM_ARCH=el8_amd64_gcc10
```
	
#### Notes

* These machines lie in a different subnet than the one that the LXPLUS machines
  belong to.
* A side-effect of the previous point is that those machines **do not
  have access to the Grid**.
* The `/nfshome0/<username>` directory is shared and available from all the machines above,
  but has limited space.
* The `/data/user/<username>` directory is not shared across the devices,
  but has larger capacity.
* [CMS Cluster Users Guide](https://twiki.cern.ch/twiki/bin/viewauth/CMS/ClusterUsersGuide)
* Use `curl` with the `--socks5` flag like so: `curl --socks5 socks5://localhost:18080 <url>`

#### Useful commands

##### Transfering files to/from P5 machines

From your own computer:

```bash
scp -r -o ProxyCommand='nc -x localhost:18080 -X 5 %h %p' <username>@gpu-c2a02-39-01.cms:/remote/path /local/path
```

This prevents the `nc: invalid option -- '-'` error.

### Special GPU nodes

??? info 

	This section is more or less taken from the [Patatrack website systems](https://patatrack.web.cern.ch/patatrack/private/systems/cmg-gpu1080.html) subpage.

#### cmg-gpu1080
	
##### System information

[Topology of the machine](https://fpantale.web.cern.ch/fpantale/out.pdf)

##### Getting access to the machine

In order to get access to the machine you should send a request to subscribe to the CERN e-group: 
[cms-gpu-devel](https://e-groups.cern.ch/e-groups/Egroup.do?egroupId=10092524).

You should also send an email to [Felice Pantaleo](mailto:felice.pantaleo@cern.ch) motivating the reason for the requested access.

##### Usage Policy

Normally, no more than 1 GPU per users should be used. To limit visible devices use

    export CUDA_VISIBLE_DEVICES=<list of numbers>

Where `<list of numbers>` can be e.g. `0`, `0,4`, `1,2,3`. Use `nvidia-smi` to check available resources.

##### Usage for ML studies

If you need to use the machine for training DNNs you could accidentally occupy all the GPUs, making them unavailable for other users.

For this reason you're kindly asked to use

`import setGPU`

before any import that will use a GPU (e.g. tensorflow). This will assign to you the least loaded GPU on the system.

It is strictly forbidden to use GPUs from within your jupyter notebook. Please export your notebook to a python program and execute it. The access to the machine will be revoked when failing to comply to this rule.



## Cloning a CMSSW release on your machine


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

### Initialize git area

``` bash
git cms-init --upstream-only
```

### Checkout a few packages using `git cms-addpkg`

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
git cms-addpkg DataFormats/SiPixelCluster/
git cms-addpkg DataFormats/SiPixelDigi/
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

Where `official-cmssw` is the remote name configured for [the CMSSW offline software repository](https://github.com/cms-sw/cmssw).

You can list your remote repositories with (for even more verbose output,
you can stack the `-v`s, e.g. `-vvv`):

```sh
git remote -v
```

## Building the code

```bash
scram b -j 8
```

!!! info
	
	See also [scram](../tools.md#scram).

## Running checks on the code

```bash
scram b code-checks
```

## Formatting the code

```bash
scram b code-format
```
