# Creating a PR to CMSSW

**Based on [http://cms-sw.github.io/tutorial.html](http://cms-sw.github.io/tutorial.html)**


**Conflict after PR**  
To resolve a conflict that appeared after proposing the changes in a PR, one should prefer **rebase** to **merge** as it keeps the commit history clean.

For a rebase, do:

```sh
git checkout <development_branch_name>
git fetch
git rebase -i <CMSSW_current_release>
```

It might be, that the tags for branches are only considered for your **default remote**, eg. my-cmssw, but the current release of CMSSW is not included in that. To resolve this, you can also try specifying the remote for the **official cmssw** (which is not a fork):

```sh
git rebase -i <official_cmssw_name>/<CMSSW_current_release>
```

## Build release

See [the Tools section for more info](tools.md#scram)

## Before making a PR

``` bash
scram b code-format # run clang-format
scram b code-checks # run clang-tidy
```
