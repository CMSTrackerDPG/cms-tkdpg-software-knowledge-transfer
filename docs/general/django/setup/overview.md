# Setting up your Django project

## Requirements

1. Python3 (ideally version>=3.8).
2. A database:
    - If using an SQlite3 database, it will be automatically created for you by Django
	- If using PostgreSQL:
		- Install PostgreSQL natively (e.g. for Ubuntu,
		instructions [here](https://ubuntu.com/server/docs/databases-postgresql)).
		- *[Optional]* Install [pgadmin4](https://www.pgadmin.org/) for administering it.
3. A virtual environment. Example setup:
	1. `cd` to your project's root.
	2. `python3 -m venv venv`
	3. `source venv/bin/activate`
	4. `python3 -m pip install -r requirements.txt`
4. Make sure you have an `.env` file with all the required project variables in
your project's root.
5. Create the database structure:
    1. `python3 manage.py migrate --run-syncdb`
		
!!! note
	
	PostgreSQL supports features that are unavailable to other
	databases, such as `ArrayFields`.
