# Local Development

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
    - `CERN_SSO_REGISTRATION_CLIENT_ID`: This is the client id for the SSO registration of the application to login users through the CERN SSO. Can be a random string if you don't intend to use CERN SSO login locally. If you need to find the actual values, go to the Application Portal and login with user `tkdqmdoc`.
    - `CERN_SSO_REGISTRATION_CLIENT_SECRET`: The client secret of the aformentioned SSO registration. Can also be a random string for local development.
    - `OMS_CLIENT_ID`: SSO registation of certhelper in order to connect to the OMS API. This specidic SSO registration can be found from the Build Env vars on PaaS. This registration has also been whitelisted by the OMS team to access the API. 
    - `OMS_CLIENT_SECRET`: Secret of aforementioned client id.
    - `SSO_CLIENT_ID`: Can be the same with `OMS_CLIENT_ID`
    - `SSO_CLIENT_SECRET`: Can be the same with `OMS_CLIENT_SECRET`.
    - `SITE_ID`: You must first add a `Sites` entry from the admin page. Then, you# can see the id by running `python manage.py shell` and then:
      ```python
      from django.contrib.sites.models import Site
      s=Site.objects.all()
      s[0]
      s[1]
      ```
      It will probably be `1`.

Then, follow the generic instructions on Django local setup, found [here](../../general/django/setup/overview.md).
