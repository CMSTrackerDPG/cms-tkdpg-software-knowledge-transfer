# FAQ

## Database management

### How do I dump and recreate a list of DQM files?

The easiest way is to just delete all `HistogramDataFiles` instances. To do so, 
you will first need to delete all data associated with these `HistogramDataFiles`.
For example, for `LumisectionHistogram2D`:

- Connect to the database via `psql` or pgadmin.
- `DELETE FROM public.histograms_lumisectionhistogram2d;` will delete
  all 2D Lumisection histogram data.
- `DELETE FROM public.historgam_file_manager where dimensionality=2` will
  delete all DQM file entries which contain 2D Lumisection histogram data.
- Exit `psql`.
- Run `python manage.py discover_dqm_files` which will
[recreate entries for the deleted DQM files](apps/histogram_file_manager/management.md).


## Deployment

### I started a new build on PaaS but it is stuck at `0 pods scaling to 1`. What's wrong?

This usually happens if the [resources](../general/openshift/resources.md)
set for the deployment are higher than the maximum available. This should not happen,
if the limits are kept under [the max ones](deploying/deployments.md#paas-resource-limits) for the MLPlayground app.
If, however, this happens, you could either:

- Wait it out for a couple of minutes.
- [MOST PROBABLE SOLUTION] Try scaling the pods down to 0 and up to 1 again.
- Set the limits to the default ones (e.g. `cpu: '1'` and `memory: '2Gi'`), and re-raise them.
