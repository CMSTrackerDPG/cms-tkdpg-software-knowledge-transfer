# ScriptExecution

Class-based views (inheriting from `DetailView`) which render
a form automatically based on the configuration created based
on a specific `RemoteScriptConfiguration` or `BashScriptConfiguration`
(the latter has not been implemented yet).

## `ScriptExecutionBaseView`

Base class-based view to be used by `RemoteScriptConfiguration`.

## `RemoteScriptView`

Inheriting from `ScriptExecutionBaseView`, it is a view
intended to render a form for executing `RemoteScriptConfiguration` instances. 

### Methods

#### `get`

Displays the dynamically-created form which is based on the `RemoteScriptConfiguration`
instance.

#### `post`

This is triggered on submitting the rendered form via `XMLHttpRequest`.

It prepares:

* The `channel_name` that the specific script should put its output to
(see also [consumers](../consumers/script_output.md)).
* The `args` and `kwargs` that were submitted with the form and parses
their values so that they can be used to execute the script
* The `kwargs` are also augmented by **callbacks**, lambda functions
that will be executed while the script is running (mainly to push specific
messages on special parts of the script execution).
* A `Thread` to execute the actual script, so that the view does not get
blocked while the script is running.

!!! note

	The script execution takes place in the `execute` method
	defined in the `RemoteScriptConfiguration` model in `models.py`
