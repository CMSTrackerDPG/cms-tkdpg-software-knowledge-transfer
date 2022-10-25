# CMSSW FAQ

## CMSSW Tools

### `fatal: unable to find remote helper for 'https'` errors

If you keep getting `fatal: unable to find remote helper for 'https'` errors
when running `git` commands, it's most probably due to an unsuccessful `cmsenv`
execution (e.g. running `cmsenv` in an more than two weeks old `CMSSW_*_*_X`version). 
Just log out and login again.
