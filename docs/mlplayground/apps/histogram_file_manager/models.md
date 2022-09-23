# Models

This app only provides the `HistogramDataFile` model.

## `HistogramDataFile`

This is a model which describes some generic attributes of the
histogram data files, such as:

- The `filepath` where they're located.
- Their `filesize`.
- The `granularity` of the data they contain (e.g. Run or Lumisection data)
- The `data_dimensionality` of the data contained (1D or 2D).

The `DATAFILE_FORMAT_CHOICES` class attribute is also defined, which is used
by [the `discover_dqm_files` management command](management.md#discover-dqm-files).
This management command is, currently, the only way to create `HistogramDataFile`
entries in the database.
