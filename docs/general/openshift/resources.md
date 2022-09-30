# Application Resources Limits

Each [OpenShift](../../basic-concepts.md#openshift) application
has a limited amount of resources it
is given access to. Exceeding these leads to spawned
[pods](../../basic-concepts.md#pod-openshift) being
killed (You might see something along the lines of `OOMKilled`).

In such a case, you can edit the application's resource
limits. The PaaS guide for tweaking those limits
can be found [here](https://paas.docs.cern.ch/6._Quota_and_resources/2-application-resources/).

!!! warning

	By default, the limits are...limited.
	See [here](https://paas.docs.cern.ch/6._Quota_and_resources/1-project-quota/)
	for details. 
	
	You may request an increase by submitting a ticket 
	[here](https://cern.service-now.com/service-portal?id=service_element&name=PaaS-Web-App).
	
