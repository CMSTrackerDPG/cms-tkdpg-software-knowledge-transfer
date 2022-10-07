# Code versioning

As of writing, [Semantic Versioning](https://semver.org/) is used for
projects like CertHelper. 

In summary:

- Versions take the form: `MAJOR.MINOR.PATCH`.
- Small changes which fix functionality which is already in place
increment the `PATCH` part.
- Changes which add new features which did not exist before increment
the `MINOR` part. Incrementing it should reset `PATCH` to `0`, e.g.
`1.0.4` --> `1.1.0`.
- Making changes to stuff that break backwards compatibility increments
the `MAJOR` part, e.g. from `1.0.3` to `2.0.0`. Incrementing the `MAJOR`
version resets both `MINOR` and `PATCH`.
