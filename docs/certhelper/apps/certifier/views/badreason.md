# badReason

Allows the user to get existing `Bad Reasons` and add new ones.
Responds in ``json`` format.

This view is triggered by clicking on the `+` sign next to the
`Bad Reasons` dropdown menu  in the `/certify/` page
(Adds new bad reason by sending a ``POST XMLHttpRequest``).

A simple ``GET`` request to the page will just return the
existing `Bad Reasons`.
