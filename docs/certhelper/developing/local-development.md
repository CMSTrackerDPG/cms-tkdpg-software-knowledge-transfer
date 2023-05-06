# Local Development

!!! warning
    
    Those instructions are tested for Ubuntu machines (up to 22.10).


To run CertHelper locally, you will need:

- A Postgres instance:
    - You can use a local instance, but you will have no data inside or have to populate it yourself, or
    - You can use the DBoD Development Database instance (hosted on `dbod-devcertdb.cern.ch`, database `developdb`). The latter requires you to either be within the CERN network or an SSH tunnel to CERN (see: `sshuttle`).
- A Redis instance, with the default settings, hosted locally, on port `6379`. Instructions to download it [here](https://redis.io/docs/getting-started/installation/install-redis-on-linux/).
- An `.env` file with the following variables inside:
    - `DJANGO_DATABASE_ENGINE=django.db.backends.postgresql`
    - `DJANGO_DEBUG=True`
    - `DJANGO_DATABASE_HOST`: change it to the appropriate PostgreSQL host.
    - `DJANGO_DATABASE_NAME=developdb` if using the instance on DBoD, else change it to whatever is the name of the database you created locally.
    - `DJANGO_DATABASE_USER`: the username for logging into the database.
    - `DJANGO_DATABASE_PASSWORD`: the password for logging into the database.
    - `DJANGO_DATABASE_PORT`: the port to connect to the database.
    - `DJANGO_SECRET_KEY`: a random string, can be anything you want.


Then, follow the generic instructions on Django local setup, found [here](../../general/django/setup/overview.md).
