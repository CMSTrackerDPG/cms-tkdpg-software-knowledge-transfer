# remotescripts app

Expanding on the `trackermaps` app, this app tries to generalize
remote script execution from certhelper, mainly to facilitate adding
and customizing execution of tools which must be run on other computers
(such as the `vocms066`).

An overview of the dataflow for executing a script can be seen
below:

```mermaid
flowchart LR
	arg1[Positional Argument 1] --> script[Script Configuration] 
	arg2[Positional Argument 2] --> script
	argdot[Positional Argument ...] --> script	
	kwarg1[Keyword Argument 1] --> script
	kwarg2[Keyword Argument 2] --> script	
	kwargdot[Keyword Argument ...] --> script		
	
	User --> View
	View -- Using script configuration --> thread[Thread]
	thread -- Execute script via SSH --> remote[Remote Machine]
	remote -- stdout --> thread
	thread -- WebSocket --> FrontEnd

```
