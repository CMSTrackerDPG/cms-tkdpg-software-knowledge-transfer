# Project configuration

Parameters which can be configured for this project.

## Environmental variables

### `DJANGO_SECRET_KEY`

### `DIR_PATH_DQMIO_STORAGE`

Path (usually to EOS) where DQM files are to be found. 
This directory is scanned for valid DQM files which are then
available to parse histograms from.

If running locally, this can be changed to whichever directory
you have sample DQM files stored.

See [here](apps/histogram_file_manager/management.md#discover_dqm_files).

### `DJANGO_DATABASE_NAME`

### `DJANGO_DATABASE_USER`

### `DJANGO_DATABASE_PASSWORD`

### `DJANGO_DATABASE_HOST`

### `DJANGO_DATABASE_PORT`

### `DJANGO_ALLOWED_HOSTS`

### `SITE_ID`

