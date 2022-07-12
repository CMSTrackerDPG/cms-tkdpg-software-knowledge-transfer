# UpdateRunView

Class-based view, inheriting from `UpdateView`, handling
a form to udpate information on existing `TrackerCertification`s.

## Methods

### `get`

The existing `get` method is overridden in order to add a
warning message once the `certify.html` template is rendered
to inform the user that they are updating an existing certification
(if they are updating their own) or are updating another user's
certification (if the user is a shift leader).

### `post`

The default `post` method is overridden to account for the
functionality existing in the [`certify` view](../../certifier/views/certify.md),
which handles editing `OmsFill` and `OmsRun` information too.

The `external_info_complete` flag is loaded from the `TrackerCertification` instance
which is being updated.

!!! warning
	
	This part of the code is copied from the `CertifyView`, 
	meaning that it kind of violates the [DRY](../../../../basic-concepts.md#dry) principle.
	:shrug:
	
	Once again, this part of the code has no business being alone in a separate app,
	but should be refactored into the [`certify`](../../certifier/overview.md)
	app at some point. 
