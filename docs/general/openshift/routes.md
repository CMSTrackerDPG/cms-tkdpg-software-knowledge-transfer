# Openshift Routes

## Configuring the gateway timeout

Long-running functions that return a response from a web-app to
a client are generally not recommended[^1]. However, in some cases
this might prove necessary.

[^1]: If a server takes too long to respond, this usually raises
a `504` HTTP error.

To do so in Openshift, locate the app's **Routes** (e.g. for
CertHelper those are [here](https://paas.cern.ch/k8s/ns/certhelper/routes)).
To set the `haproxy`'s (which handles the routing) timeout, add an 
**annotation** to the Route:

1.  Click on **Edit annotations**:

	![Dropdown menu with the `Edit annotations` menu visible](img/edit_annotations.png)
   
2. Add a new key named `haproxy.router.openshift.io/timeout` with value the required timeout
in either seconds or minutes, e.g. `5m`:

	![Add annotation](img/add_annotation.png)
