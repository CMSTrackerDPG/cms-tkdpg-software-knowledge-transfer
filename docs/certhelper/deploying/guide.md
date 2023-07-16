# Deployment Guide

The following steps will guide you through the deployment procedure of the app on OpenShift.
An overview of the steps is:

1. [Create a new PaaS project](./#requesting-a-website)
2. [Setup the repository that will be used](./#setup-procedure)
3. [Setup Environmental Variables](./#setup-environmental-variables)
4. [Setup a Database](./#setup-a-database)
5. [Mount EOS Storage](./#mount-eos-storage)
6. [Add Redis Server](./#add-redis-server)
7. [Add nginx Server](./#add-nginx-server-not-working-for-now) (not working for now)
8. [Single Sign-On](./#single-sign-on)
9. [Deploy](./#deploying-a-new-build)
10. [Expose the app](./#exposing-the-app)

The procedure can be done completely via the web UI provided by PaaS. However,
the `oc` command line utility can prove very useful.
See [below](#oc-command-line-utility)
for instructions on how to install it.

## Prerequisites

### `oc` command line utility

!!! note
	
	Optional

[Download and install](https://paas.docs.cern.ch/1._Getting_Started/5-installing-cli/)
the `oc` command line utility, preferably on your lxplus account.

### `.s2i` directory inside the root of your repository

We will be using the Software To Image ([s2i](../../basic-concepts.md#s2i-source-to-image))
approach to deploy on PaaS,
namely the [Python flavor](../../basic-concepts.md#python-s2i).
This means that a Docker image is created from our repository on each deployment.

There should be a ``.s2i`` directory inside your repository, with the ``environment`` file in it.
Inside the ``.s2i`` directory, make sure there is a ``bin`` directory with an ``assemble`` file in
it.

#### ``environment`` contents

These are environmental variables used by Openshift when creating the
Docker image. The value of ``APP_SCRIPT`` will be the entrypoint of the created image.

```bash
DISABLE_COLLECTSTATIC=true
APP_SCRIPT=openshift-start-up-script.sh
```

#### ``assemble`` contents

This will override the ``assemble`` stage of the s2i procedure, so that
we can do configuration as needed. Most important configuration change is
CERN gitlab authentication which is required to ``pip install`` from private
repositories.

```bash
   
#!/bin/bash
echo "Before assembling"
git config --global url."https://$CERN_GITLAB_USER:$CERN_GITLAB_TOKEN@gitlab.cern.ch".insteadOf https://gitlab.cern.ch
/usr/libexec/s2i/assemble
rc=$?

if [ $rc -eq 0 ]; then
  echo "After successful assembling"
else
  echo "After failed assembling"
fi

exit $rc
```

!!! note
	
	See [Setup Environmental Variables](#setup-environmental-variables)
	for the required environmental variables used in the script above.

## Requesting a website

Create a new PaaS project by clicking
[here](https://paas.docs.cern.ch/1._Getting_Started/1-create-paas-project/).
Then, fill out the fields as shown below:

![request website](img/request-website.png)

When creating a website, different site types can be chosen. In
order to use the OpenShift software, the `PaaS Web Application` option
has to be selected.

## Setup Procedure

Once the website is successfully requested the application should be
available in OpenShift. Following steps need to be done in order to
configure the web application with the GitHub repository:

1.  Go to [PaaS](https://paas.cern.ch).
2.  Select the project you created

	![](img/paas-select-project.png)
	
3.  Click on "Add" on the left

	![](img/paas-add.png)
	
4.  choose ``Git Repository``

	![](img/paas-add-git.png)
	
5.  Paste the repository URL in the field provided.
6.  Under ``Advanced Git options``, you may select a specific branch, if needed. E.g. for the 
training certhelper instance, the `training` branch must be selected.
7.  okd will automatically detect that this is a Python	application and will select the latest version of Python.

	!!! info
		
		As of writing, we select ``3.8-ubi8``.
	   
8.  Under **General**, change the **Application name** and **Name** appropriately. 
9.  Under **Resources**, select **Deployment**

	![](img/paas-deployment.png)
			   
10. [Optional] Add GitHub credentials at "Source Secret" *if the repository is
    private*
	
11. Make sure that *Create a route to the Application* is ticked.
12. Under *Show advanced Routing options*:
	a. Paste the **Hostname** you want (will be automatically registered),
	b. Make sure *Secure Route* is ticked,
	c. Under **TLS termination**, select `Edge`,
	d. Under **Insecure Traffic**, select `Redirect`.
13. Click on **Create**. The application has been configured!

	!!! note
		
		Under **Topology**, you will see your project trying to run for the first time.
		This will fail, since most environmental variables are missing. Click on the
		main app:
		
		![](img/paas-topology-main-app.png)
		
		You should be getting the following error:
		
		![](img/paas-crash-loop.png)

## Setup Environmental Variables

1. Under `Builds --> Your project name --> Environment` use the `Add more` and `Add from ConfigMap or Secret` buttons to add the variables:

	1. Accounts/Secrets environment variables (added using `Add Value from Config Map or Secret` button):
	
		* Database credentials:
	  
		```
		DJANGO_SECRET_KEY          <your-secret>
		DJANGO_DATABASE_USER       <your-username>
		DJANGO_DATABASE_PASSWORD   <your-password>
		```
	  
		* Email notifications:
	  
		```
		DJANGO_EMAIL_HOST_USER     <your-email-username>
		DJANGO_EMAIL_HOST_PASSWORD <your-email-password>
		```
	  
		* Tracker Maps credentials:
	  
		```
		DJANGO_SECRET_ACC           <account-username>
		DJANGO_SECRET_PASS          <account-password>
		```
	  
	2. Remaining Variables (added using `Add Value` button):
		* Needed for OpenShift to be able to access the site:

		```
		DJANGO_ALLOWED_HOSTS       <Host website you registered in step 12.a>
		DJANGO_DEBUG               False
		```
	  
		* this will be used for the database credentials:
		
		```
		DJANGO_DATABASE_ENGINE     django.db.backends.postgresql_psycopg2
		DJANGO_DATABASE_NAME       <your-database-name>
		DJANGO_DATABASE_HOST       <your-database-host>
		DJANGO_DATABASE_PORT       6611
		```
		  
		* this will be used for the email notifications:
		
		```
		DJANGO_EMAIL_HOST          smtp.cern.ch
		DJANGO_EMAIL_PORT          587
		DJANGO_EMAIL_USE_TLS       True
		DJANGO_SERVER_EMAIL        <tkdqmdoctor-email-address>
		```
		  
		* this will be used for the cernrequest and Runregistry API:

		```
		CERN_CERTIFICATE_PATH       <path>
		```
			  
		* this will be used to access the Redis server (secret is created
		automatically by the redis yaml):

		```
		REDIS_HOST                  <redis-[server number]>
		REDIS_PASSWORD              <password>
		```

	3. Other:

	```
	CSRF_TRUSTED_ORIGINS        https://[the hostname you resistered in step 12a]
	```
	
	4. To access CERN's gitlab private repositories via ``pip``:
	
	```
	CERN_GITLAB_USER           <CERN gitlab username>
	CERN_GITLAB_TOKEN          <CERN gitlab access token with read_repository permissions>
	```

2. Save the variables and rebuild the project:
	![](img/paas-rebuild.png)

	You should now be able to visit the app on the URL you specified.
		
!!! note

	The procedure above should only be followed once.
	Once the app is fully configured, you should not have to alter anything,
	unless a change occurs (e.g. Database host/password).

## Setup a Database

The database was requested from the CERN
[DB on demand service](https://dbod.web.cern.ch/). To request one, 
follow the instructions [here](https://dbod-user-guide.web.cern.ch/instance_creation/).

A PostgreSQL database is used.

After the database has been requested it can be used straight away.
Django takes care of creating the necessary tables and only requires the
credentials.

### Actions to take once the database is ready

#### Change default password

Given the username that was sent to you via the DBoD Service,
(possibly `admin`) connect to the database:

```bash
psql -h dbod-birdup.cern.ch -U admin -p 6601
```

And run:

```sql
ALTER ROLE admin WITH PASSWORD 'new_password';
```

#### SSL Configuration

!!! error

	``` bash
	django.db.utils.OperationalError: connection to server at 
	"<host>" (<ip>), port 6601 failed: FATAL:  no pg_hba.conf entry for host "<ip>"
	```

Follow the instructions [here](https://dbod-user-guide.web.cern.ch/instance_management/ssl/postgres/)
to edit the required configuration files using
[the file editor](https://dbod-user-guide.web.cern.ch/instance_management/configuration_files/).

#### Create the database

Assuming that the [database name](#setup-environmental-variables)
you are going to use is `certhelperdb`
(i.e. `DJANGO_DATABASE_NAME` is `certhelperdb`), you will need to 
create it manually first.

To do so, you will have to first connect to it using `psql`[^1]:
[^1]: You will either have to do that through LXPLUS, or your computer must
	be inside CERN. You can always use an SSH tunnel for that.


```bash
psql -h <Database hostname> -p <Database port> -U <Database user>
```

Then, in the SQL prompt, run the following to create the database:

```sql
CREATE DATABASE certhelperdb;
```

and enter your password once prompted.

## Mount EOS Storage

### Via the UI

!!! info

	Recommended

The project has 1 TB of storage associated in the EOS. To mount it to
OpenShift follow these instructions.

Detailed instructions can be found on the
[PaaS docs](https://paas.docs.cern.ch/3._Storage/eos/).

### Using ``oc``

!!! warning
	
	Might be deprecated

#### Create Secret

Replace with your password.

```bash
oc create secret generic eos-credentials --type=eos.cern.ch/credentials --from-literal=keytab-user=tkdqmdoc --from-literal=keytab-pwd=<the-password>
```

#### Do EOS stuff

Run these commands and replace with the name of your build.

```bash
oc set volume dc/<your-build-name> --add --name=eos --type=persistentVolumeClaim --mount-path=/eos --claim-name=eos-volume --claim-class=eos --claim-size=1

oc patch dc/<your-build-name> -p "$(curl --silent https://gitlab.cern.ch/paas-tools/eosclient-openshift/raw/master/eosclient-container-patch.json)"

oc set probe dc/<your-build-name> --liveness --initial-delay-seconds=30 -- stat /eos/project/t/tkdqmdoc

oc set probe dc/<your-build-name> --readiness -- stat /eos/project/t/tkdqmdoc
```

if it gets stuck or you encouter some errors on openshift like:

`Readiness probe failed: stat: cannot stat '/eos/project/t/tkdqmdoc': No such file or directory`

then rerun all 4 commands again:

```bash

oc set volume dc/<your-build-name> --add --name=eos --type=persistentVolumeClaim --mount-path=/eos --claim-name=eos-volume --claim-class=eos --claim-size=1

oc patch dc/<your-build-name> -p "$(curl --silent https://gitlab.cern.ch/paas-tools/eosclient-openshift/raw/master/eosclient-container-patch.json)"

oc set probe dc/<your-build-name> --liveness --initial-delay-seconds=30 -- stat /eos/project/t/tkdqmdoc

oc set probe dc/<your-build-name> --readiness -- stat /eos/project/t/tkdqmdoc
```
Then start the built and it should work.

!!! tip

	For deleting the volume run the following command first

	```bash
    kubectl patch pvc PVC_NAME -p '{"metadata":{"finalizers": []}}' --type=merge
	```

#### Add shared volume

Add a shared volume to allow the use of unix socket between nginx and daphne

```bash
oc set volume dc/<your-build-name> --add --name=<volume-name> --type=persistentVolumeClaim --mount-path=<path> --claim-name=<volume-name> --claim-class=cephfs-no-backup --claim-size=1
```

## Add Redis Server

A redis server will used by the `channels-redis` module as a backing store. 

Navigate to `Topology` and right-click next to the pod of the project.
Then, click `Add to Project --> From Catalog`.

![](img/paas-add-from-catalog.png)

Then, search for and select `Redis`, and then `Instantiate Template`. 

![](img/paas-redis.png)

Choose `5-el8` as the `Version of Redis Image`. `6-el8` was tested
but did not work as of writing (2022/07).
Leave all other settings to their default values. Take note of the `Database Service Name`,
which will serve as the hostname that Django will have to connect to.

Click on `Create`. This will automatically place a new pod on the
topology, which is effectively a separate system running a redis server.

Verify that by navigating to `Secrets`, a new ``redis`` secret which has been created.

Now, navigate to `Developer --> Builds --> <Your Project> --> Environment`
and add two new values:

- Click on `Add more` and name the new key ``REDIS_HOST``.
Its value must be equal to the hostname you noted earlier.
- Click on `Add from ConfigMap or Secret` and name the new key ``REDIS_PASSWORD``.
Its value must be the `redis --> database-password` secret.

Rebuild the main project and, by connecting to Tracker Maps, you should not be
getting any errors in the Django logs.

## Add nginx Server (not working for now)

!!! warning

	Not tested/Deprecated

1.  Go to [the webconsole](https://openshift.cern.ch/console/)
2.  choose "Nginx HTTP server and a reverse proxy (nginx)"
3.  click `Next`
4.  select your project in `Add to Project`
5.  choose a name
6.  add this [git repository](https://github.com/alingrig/nginx-ex)
7.  click `Create`
8.  add the shared volume

```bash
oc set volume dc/<your-chosen-name> --add --name=<volume-name> --type=persistentVolumeClaim --mount-path=<path> --claim-name=<volume-name> --claim-class=cephfs-no-backup --claim-size=1
```

9.  go to `Application --> Routes`
10. replace the dev-certhelper route with an one for nginx-server

## Single Sign-On

### CERN Setup

OIDC is an authorization service which can be used to authenticate
CERN users. The advantage of using such an authorization service is that
users of the certification helper do not have register manually, but can
already use their existing CERN accounts.

* Visit the [application portal](https://application-portal.web.cern.ch/).
* Edit your application (e.g. `webframeworks-paas-certhelper`).
* Click on `SSO Registration` and generate an `OpenID Connect` provider.
	* For `redirect_uri`, use `https://certhelper.web.cern.ch/accounts/cern/login/callback/` for
	the production website and
	`https://dev-certhelper.web.cern.ch/accounts/cern/login/callback/`
	for the development site.
* Note the `Client ID` and `Client Secrets`.


!!! note

	Each instance of certhelper (production, development, training) requires a different SSO registration
	key, so you cannot reuse an existing `client_id` and `secret` for a new
	instance.


### Integration

The single sign-on integration is very easy when using the
`django-allauth` python package, which has built-in OIDC support.

Follow the installation procedure
[here](https://django-allauth.readthedocs.io/en/latest/installation.html).

Certhelper uses the `openid_connect` provider. There used to be CERN
integration with `django-allauth`, but this has been deprecated after
CERN's migration to a new SSO solution, and updating was [proved to be reduntant](https://github.com/pennersr/django-allauth/pull/3348).
In its place, the `openid_connect` provider works fine.


!!! note "Notes on the installation procedure"

	* When adding a `Site`, use the complete URL of the app (e.g. `https://certhelper.web.cern.ch/`).
	* Just in case, visit `/admin/socialaccount/socialapp/1/change/` to add the Client ID and secret there too.
	* Verify the `SITE_ID` value by checking the database itself. E.g. it might ge `1` or `2`
	* Add `ACCOUNT_EMAIL_VERIFICATION = "none"` in `settings.py` to disable sending 
	a verification email on first signup.
	

## Deploying a new build

### Production Site (``certhelper``)

If you want to rebuild the production website (``master`` branch) you can
do so manually by
[triggering a build on PaaS](https://paas.cern.ch/k8s/ns/certhelper/build.openshift.io~v1~BuildConfig).

This can be done by visiting
[paas.cern.ch](https://paas.cern.ch/), selecting the
``certhelper`` project and then visiting `Build --> builds`. This
page should already contain a build of the Certification Helper project that is
automatically pulled from GitHub. By clicking on this build and then
pressing the `build` button the whole deployment process should be
started. In the meantime, the logs of the build process can be viewed by
clicking on `View Log`.

### Deployment Schedule

The main `certhelper` instance should only be deployed on shift changes,
to prevent confusion and/or any inconvenience.
Contact a Shift Leader to verify the schedule.

The `training-certhelper` instance should also be updated whenever no training
is taking place.

The `dev-certhelper` instance can be updated at will.

To automate deployment, use OpenShift's `CronJobs` to create pods based on the `curlimages/curl` image:

* Navigate to the project's [BuildConfig](https://paas.cern.ch/k8s/ns/certhelper/buildconfigs/certification-helper), find the `Generic` webhook shown at the bottom of the page and click `Copy URL with Secret`.
* Navigate to `Administrator --> Workloads --> CronJobs`
([link](https://paas.cern.ch/k8s/ns/certhelper/cronjobs)) and create a new `CronJob`.
* Update `name` under `metadata` to something meaningful (e.g.: `scheduled-deployment`)
* Update `schedule` under `spec` to the desired crontab (e.g.: `'0 0 * * 1,5'`, time is in UTC)
* Use `curlimages/curl` as `image`
* Under ``args`` paste:
```yaml
args:
    - curl
	- '-X'
	- POST
	- '-k'
	- >-
		<the Generic Webhook you copied earlier>
```

A new pod will be created under the crontab schedule you configured, triggering a new build.

!!! warning

    It is generally not a good idea to have the CronJob running at all times, mainly due to the fact that PyPI libraries may be updated at any time; automatic deployment with the latest available libraries is going to be a risk, as they new versions may well contain bugs. 
    
    A safe approach is to enable the CronJob by setting `suspend: false` (see [here](https://docs.openshift.com/container-platform/3.11/dev_guide/cron_jobs.html#creating-a-cronjob)) only once you have pushed changes to the certifier repository. Then, once the changes are deployed, set `suspend: true` again.

## Exposing the app

See the [PaaS docs](https://paas.docs.cern.ch/5._Exposing_The_Application/2-network-visibility/)
on how to make the app visible from outside the CERN GPN.
