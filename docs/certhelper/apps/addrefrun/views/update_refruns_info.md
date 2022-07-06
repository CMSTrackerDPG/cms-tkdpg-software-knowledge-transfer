# update_refruns_info

A view which triggers an update of the information of each
[`OmsRun`](../../oms/models.md) which is related to any `RunReconstruction`
which is a reference one (`is_reference=True`).

This was added due to the fact that some `OmsRun` information
(namely `APV_mode`) is only available through a specific external
web app ([ebutz.web.cern.ch](https://ebutz.web.cern.ch/)). Initially, 
the app was queried on `OmsRun` instance creation (by overriding the
`save` method), but this proved to be too slow, especially when running
tests.

Therefore, this functionality is only triggered on demand, via a button
on the `/reference/` URL of CertHelper.
/
