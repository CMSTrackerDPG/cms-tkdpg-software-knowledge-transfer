# Application Resources Limits

Each [OpenShift](../../basic-concepts.md#openshift) application
has a limited amount of resources it
is given access to. Exceeding these leads to spawned
[pods](../../basic-concepts.md#pod-openshift) being
killed (You might see something along the lines of `OOMKilled`).
