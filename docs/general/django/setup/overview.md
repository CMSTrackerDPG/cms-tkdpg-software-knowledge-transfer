# Setting up your Django project locally

!!! warning 
	
	This guide is intended for a Linux-based OS

## Requirements

1. **Python3** (ideally version>=3.8).
2. A **database:**
    - If using an SQlite3 database, it will be automatically created for you by Django
	- If using PostgreSQL:
		- Install PostgreSQL natively (e.g. for Ubuntu,
		instructions [here](https://ubuntu.com/server/docs/databases-postgresql)).
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
9. **Run the developement server:** `python3 manage.py`. By default, you should
be able to access [http://localhost:8000](http://localhost:8000). 
		
!!! note "Why PostgreSQL?"
	
	PostgreSQL supports features that are unavailable to other
	databases, such as `ArrayFields`.
	
!!! note "Logging in"
	
	When running the project locally, CERN SSO will not be functioning,
	so use the superuser you created to login to the app.
	
!!! note "Usual workflow"

	The setup procedure needs to be followed only once, usually.
	For usual developement, you will only need to run steps **4 and 9**.
