# Setting up your Django project locally

!!! warning 
	
	This guide is intended for a Linux-based OS
	


## Procedure

0. `git clone` your django project locally.
1. Verify that **Python3** is installed on your system (ideally version>=3.8).
2. Create a **database:**
    - If using an *SQlite3* database (Django's default), it will be automatically
	created for you by Django in the following steps.
	- If using *PostgreSQL*:
		- Install PostgreSQL natively (e.g. for Ubuntu,
		instructions [here](https://ubuntu.com/server/docs/databases-postgresql)).
		- Add a password for the `postgres` user as per the instructions above.
		- Create the database:
		    - Connect: `psql -U postgres`
			- `CREATE DATABASE <database name>;` where `<database name>` is the database
			name specified in Django's settings, or `.env` file, e.g. `mlplayground_development`.
		- *[Optional]* Install [pgadmin4](https://www.pgadmin.org/) for administering it.
3. A **virtual environment.** Example setup:
	1. `cd` to your project's root.
	2. `python3 -m venv venv`
4. **Activate** the virtual environment:
	- `source venv/bin/activate` 
5. Install **requirements:**
	- `python -m pip install -r requirements.txt` (once the venv is activated
    you don't need to specifically run `python3`, just `python`).
6. Make sure you have an **`.env`** file with all the required project variables in
your project's root.
7. Create the **database structure:**
    - `python manage.py migrate --run-syncdb`
8. **Create a superuser**:
	- `python manage.py createsuperuser`
9. **Run the developement server:** `python3 manage.py runserver`. By default, you should
be able to access [http://localhost:8000](http://localhost:8000).
    - If you want to run **with HTTPS:**
        - `pip install Werkzeug`
        - `python manage.py runserver_plus --cert cert`
		
!!! note "Why PostgreSQL?"
	
	PostgreSQL supports features that are unavailable to other
	databases, such as `ArrayFields`.
	
!!! note "Logging in"
	
	When running the project locally, CERN SSO will not be functioning,
	so use the superuser you created to login to the app.
	
## Usual workflow

The setup procedure needs to be followed only once, usually.
For usual developement, you will only need to run steps [**5 and 10**](#procedure).

