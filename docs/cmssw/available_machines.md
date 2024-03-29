# Available machines

As an overview, you have three choices in order to run GPU-enabled code:

- The very heavily-used [`lxplus-gpu`](#lxplus-gpu) machines (**[NOT RECOMMENDED]**).
- The Point5's [Online Machines](#cms-point-5-machines) ({==RECOMMENDED==}).
- [Special nodes](#special-gpu-nodes).

## lxplus-gpu

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

## CMS Point 5 Machines

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

### Request a CMS Online account

To request access, please subscribe to the [cms-hlt-gpu](https://e-groups.cern.ch/e-groups/Egroup.do?egroupId=10346110&searchField=0&searchMethod=0&searchValue=cms-hlt-gpu&pageSize=30&hideSearchFields=false&searchMemberOnly=false&searchAdminOnly=false) e-group and send an email to [andrea.bocci@cern.ch](mailto:andrea.bocci@cern.ch), indicating:

* whether you already have an online account;
* your online or lxplus username;
* your full name and email.

### How to connect

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

### Special configuration required

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
	
### Notes

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

### Useful commands

#### Transfering files to/from P5 machines

From your own computer:

```bash
scp -r -o ProxyCommand='nc -x localhost:18080 -X 5 %h %p' <username>@gpu-c2a02-39-01.cms:/remote/path /local/path
```

This prevents the `nc: invalid option -- '-'` error.

## Special GPU nodes

??? info 

	This section is more or less taken from the [Patatrack website systems](https://patatrack.web.cern.ch/patatrack/private/systems/cmg-gpu1080.html) subpage.

### cmg-gpu1080
	
#### System information

[Topology of the machine](https://fpantale.web.cern.ch/fpantale/out.pdf)

#### Getting access to the machine

In order to get access to the machine you should send a request to subscribe to the CERN e-group: 
[cms-gpu-devel](https://e-groups.cern.ch/e-groups/Egroup.do?egroupId=10092524).

You should also send an email to [Felice Pantaleo](mailto:felice.pantaleo@cern.ch) motivating the reason for the requested access.

#### Usage Policy

Normally, no more than 1 GPU per users should be used. To limit visible devices use

    export CUDA_VISIBLE_DEVICES=<list of numbers>

Where `<list of numbers>` can be e.g. `0`, `0,4`, `1,2,3`. Use `nvidia-smi` to check available resources.

#### Usage for ML studies

If you need to use the machine for training DNNs you could accidentally occupy all the GPUs, making them unavailable for other users.

For this reason you're kindly asked to use

`import setGPU`

before any import that will use a GPU (e.g. tensorflow). This will assign to you the least loaded GPU on the system.

It is strictly forbidden to use GPUs from within your jupyter notebook. Please export your notebook to a python program and execute it. The access to the machine will be revoked when failing to comply to this rule.
