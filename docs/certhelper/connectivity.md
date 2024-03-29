# CertHelper's connectivity

During its operation, CertHelper will try to connect to various remote
resources. An overview can be seen in the sketch below. 

```mermaid
flowchart
	db[(DBoD)] <-- TCP --> ch[CertHelper]
	vocms[vocms066] <-- SSH --> ch
	ebutz[ebutz] <-- HTTP --> ch
	rr[RunRegistry] <-- HTTP --> ch
	oms[OMS API] <-- HTTP --> ch
	redis[Redis] <-- TCP --> ch

```

## Storage

- PostgreSQL database (hosted by CERN's [DBoD service](https://dbod.web.cern.ch/))(__required__)
- Redis server, as a backing store for Websockets (Deployed on Openshift).

## APIs

- [OMS API](https://vocms0185.cern.ch/agg/api), for getting run and fill information
(in [`omsapi` app](./apps/omsapi/overview.md) and [`oms/utils.py`](./apps/oms/overview.md) as of writing)
- [CMS Run Registry](https://cmsrunregistry.web.cern.ch/), for getting
reconstruction and dataset information (in `oms/utils.py` as of writing)
- [ebutz's](http://ebutz.web.cern.ch/ebutz/cgi-bin/getReadOutmode.pl) personal
tools, for getting extra information for Reference Runs (in
[`oms/models.py`](./apps/oms/models.md) as of writing)

## Tools

- Any remotescript configuration (such as the Trackermaps generation script on `vocms066.lxplus.cern.ch`)
accesses available remote hosts as configured (see [`remotescripts`](apps/remotescripts/overview.md)).
