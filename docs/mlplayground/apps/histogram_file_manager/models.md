# Models

This app only provides the `HistogramDataFile` model.

## `HistogramDataFile`

This is a model which describes some generic attributes of the
histogram data files, such as:

- The `filepath` where they're located.
- Their `filesize`.
- Their `contents`, which is a `ManyToMany` field to the `HistogramDataFileContents` table. A single
DQM file may contain mutltiple types of histograms (e.g. `LumisectionHistograms1D` and `LumisectionHistograms2D`).

The `DATAFILE_FORMAT_CHOICES` class attribute is also defined, which is used
by [the `discover_dqm_files` management command](management.md#discover-dqm-files).
This management command is, currently, the only way to create `HistogramDataFile`
entries in the database.

Currently, the choices for available files provided are only refreshed
every time the `discover_dqm_files` management command is run. To run it, login to
PaaS, select the `ml4dqm-playground` project, go to `Administrator`->`Pods`, select
the currently running pod, go to `Terminal` and run `python manage.py discover_dqm_files`

!!! warning

	The `HistogramDataFile` entries can be deleted without affecting the
	Histograms loaded from the deleted files. However, re-reading the
	file will **NOT** update the existing Histogram entries to point to
	the newly read file
	
	This is due to the `HistogramBase` model having the `on_delete=SET_NULL` on
	the `ForeignKey` to the `HistogramDataFile`. 
	
	This was chosen so that the large number of extracted lumisections would be
	preserved in the DB after deleting an entry to a `HistogramDataFile`.

## `HistogramDataFileContents`

- The `granularity` of the data they contain (e.g. Run or Lumisection data)
- The `data_dimensionality` of the data contained (1D or 2D).
