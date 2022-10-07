# FAQ

## How do I dump and recreate a list of DQM files?

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
