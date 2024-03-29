# Management commands

!!! note

	Run these commands on [a PaaS terminal](../../deploying/guide.md#open-a-terminal-in-the-running-pod).

## `discover_dqm_files`

Location: `histogram_data_files/management/commands/disover_dqm_files.py`

A django management command which is responsible for searching through
the directory pointed to by the
[`DIR_PATH_DQMIO_STORAGE` environment variable](../../config.md#dir_path_eos_cmsml4dc),
keeping only those with a [valid file extension](models.md#histogramdatafile).

For each valid file found, a new entry in the database is created. 

!!! note

	This procedure **does not** parse the files' contents, it just
	creates an entry for the files themselves in the database. 
	
	The parsing is handled by the appropriate model methods found
	in the `histograms/models.py` file and is invoked via the [API](api.md#start_parsing).
