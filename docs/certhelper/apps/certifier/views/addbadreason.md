# addBadReason

A simple form served at `/certify/addbadreasonform/` so that it can
be loaded using jQuery (using ``load()``) on the `/certify/` page.

The form to submit a new bad reason is dynamically loaded via jQuery
from the `/certify/addbadreasonform/` URL and stored in the ``div``
with ``id="addBadReason"``. When the button to submit a new bad
reason is pressed, a ``POST XMLHttpRequest`` is made to
`/certify/badreasons/` with the ``name`` and ``description`` of
the new bad reason. A ``json`` response is returned.
