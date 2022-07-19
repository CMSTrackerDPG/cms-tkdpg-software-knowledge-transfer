# Obsolete code

##  List of central certification runs

On the `Weekly Certification` tab (`shiftleader/templates/shiftleader/weekly-cert.html`),
a form field with id `id-cc-input`
triggers a `keyup` event (`shiftleader/static/shiftleader/js/shiftleader.js`) which
makes an AJAX request to an non-existent `ajax/validate-cc-list/` URL with a
`text` parameter which is assigned the contents of the form field.

As of writing, no documentation was found regarding this AJAX call so this
functionality was removed altogether.
