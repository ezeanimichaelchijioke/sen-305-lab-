# VUA-SEN305 — In-Lab Exercise (Service Request Extension)

This package implements the three tasks in the supplied exercise:
- `due_at` plus `(status, due_at)` index and an overdue Admin filter
- `resolution_summary` plus model-level validation for RESOLVED/CLOSED
- synthetic data command and query/EXPLAIN evidence script
- automated model validation tests

## Important note about the starting project
The practical note describes an existing Lab 3 project, but that project folder was not supplied.
Therefore this ZIP is a runnable reference project reconstructed from the field list and conventions
in the practical note. Its `0001_initial.py` represents the Lab 3 baseline; `0002_service_level_due_date_and_resolution_summary.py`
is the new extension migration. If you already have a Lab 3 repository, copy the app changes and
the new migration into it after comparing your actual model and migration state. Do not replace
your own existing `0001_initial.py`.

## Requirements
- Python 3.10+
- Django 5.2
- MySQL 8 for the lab database (SQLite is used by default for quick local tests)

## Windows setup (PowerShell)
```powershell
cd backend
py -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python manage.py check
python manage.py showmigrations requests_app
python manage.py sqlmigrate requests_app 0002
python manage.py migrate
python manage.py test requests_app.tests -v 2
```

The default settings use SQLite so you can run the tests without configuring MySQL.
To use MySQL, set environment variables before running Django:
```powershell
$env:DB_ENGINE="mysql"
$env:DB_NAME="sen305_service"
$env:DB_USER="root"
$env:DB_PASSWORD="your_password"
$env:DB_HOST="127.0.0.1"
$env:DB_PORT="3306"
python manage.py migrate
```
Create the database first in MySQL:
```sql
CREATE DATABASE sen305_service CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

## Inspect migration discipline
`0001_initial.py` is the baseline migration in this reference package.
The exercise extension is isolated in `0002_service_level_due_date_and_resolution_summary.py`.
Review it with:
```powershell
python manage.py sqlmigrate requests_app 0002
python manage.py migrate requests_app 0002
python manage.py showmigrations requests_app
```

## Admin
Create an admin user with `python manage.py createsuperuser`, then visit `/admin/`.
The ServiceRequest list displays due date and resolution summary and includes an overdue filter.

## Synthetic data and query analysis
Run:
```powershell
python manage.py seed_lab_data
```
Then:
```powershell
python manage.py shell < scripts/query_analysis.py
```
The script prints the generated SQL and the database's EXPLAIN output. It also explains that
MySQL may choose a table scan for a tiny synthetic table. The new index is for `(status, due_at)`;
it is not a composite index for category/status/priority ordering.

Only synthetic laboratory records are created by the seed command.
