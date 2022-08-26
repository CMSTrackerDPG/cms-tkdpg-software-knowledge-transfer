# `SummaryView`

A class-based view which handles both `GET` and `POST` requests.

## `GET`

When the `summary/` page is requested with `GET`, the filters
supplied in the URL are checked. If no filters are supplied, 
the current day is used to filter `TrackerCertification` instances.

Then, only the requesting user's certifications are kept. 

Using the queryset that has been created (`runs`), a unique
`SummaryInfo` instance is created using `get_or_create`.

Then, a `form` is created, which, if the specific `SummaryInfo` instance
did not exist, is empty. If the instance already existed, the form is
pre-populated with the data from the `SummaryInfo` instance.

Finally, the HTML template is rendered.

## `POST`

In the generated summary, there are two fields (supplied by the
`SummaryExtraInfoForm`) which are manually entered by the shifter
(`links_prompt_feedback` and `special_comment`). Once
the shifter submits either of those fields, a `POST` request is made
by the front-end (via `ajax`) which updates the two fields in the
`SummaryInfo` instance.

In order to do that, the `certs_list` list is passed in the context of the 
template, which is included in the `ajax` request, so that the `post` method
of the view can identify which `SummaryInfo` instance to update. 

!!! info

	This should maybe be done with an `UpdateView` in the future. 
	As the feature was implemented hastily, I just refactored the old
	code subclassing a simple `View`.
