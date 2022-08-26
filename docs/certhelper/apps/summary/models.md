# summary app models

## `SummaryInfo`

Contains extra information provided by the shifter on Summary generation:

- Links to Prompt Feedback prompts
- Special comments

Each `SummaryInfo` instance is identified by a unique combination of
`TrackerCertification` instances: When a shifter generates a report, in 
the backend a `TrackerCertification` queryset is created which filters
the certifications done by the specific user, for a specific day.

The primary keys of this exact queryset (i.e. the `runreconstruction` field, which
is actually the primary key of `RunReconstruction` instances) is made into
a list, which is stored in the `certifications` fields of the `SummaryInfo` instances.

This means that a list of `RunReconstruction` primary keys is the primary key of each 
`SummaryInfo`.

This was chosen instead of using a `ManyToManyField`, because each summary has to be
unique for each combination of `TrackerCertification`s, and `ManyToManyField` cannot
have `unique=True`.


!!! warning
	
	There are two implications regarding this choice:

	- The underlying database must always be **PostgreSQL** since it's
	the only one supporting `ArrayFields`
	- If a `TrackerCertification` is deleted, the corresopnding summary will not be automatically
	deleted, meaning that there may be summaries that refer to non-existent certifications in the
	long run.

This information is used when the Shiftleader report or presentation are rendered
(in the day-by-day certification).


