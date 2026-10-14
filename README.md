# Django REST Framework Backend

A Django REST Framework backend project with JWT authentication, API documentation, PostgreSQL support, environment-based configuration, testing, and code linting.

## Prerequisites

- Python stable release (latest stable version compatible with your dependencies)
- pip (Python package manager)
- PostgreSQL
- Git

## 1. Install Python

Download Python from the official website:

https://www.python.org/downloads/

Verify the installation:

```bash
python --version
pip --version
```

## 2. Create a Virtual Environment

Create an isolated Python environment for the project:

```bash
python -m venv venv
```

## 3. Activate the Virtual Environment

**Windows — Command Prompt:**

```cmd
venv\Scripts\activate.bat
```

**Windows — PowerShell:**

```powershell
.\venv\Scripts\Activate.ps1
```

**Linux / macOS:**

```bash
source venv/bin/activate
```

After activation, your terminal should display `(venv)`.

## 4. Upgrade pip

```bash
python -m pip install --upgrade pip
```

## 5. Install Django and Django REST Framework

```bash
pip install django djangorestframework
```

## 6. Install Authentication and API Documentation Packages

```bash
pip install djangorestframework-simplejwt drf-spectacular
```

- **Simple JWT:** JWT-based authentication.
- **drf-spectacular:** OpenAPI schema generation and Swagger/ReDoc documentation.

## 7. Install Configuration and CORS Packages

```bash
pip install django-environ django-cors-headers
```

- **django-environ:** Loads configuration from environment variables.
- **django-cors-headers:** Configures Cross-Origin Resource Sharing (CORS).

## 8. Install Database and Production Server Packages

```bash
pip install "psycopg[binary]" gunicorn
```

- **psycopg:** PostgreSQL database adapter.
- **Gunicorn:** Production WSGI server for supported deployment environments.

## 9. Install Development and Testing Packages

```bash
pip install pytest pytest-django ruff
```

- **pytest:** Testing framework.
- **pytest-django:** Django integration for pytest.
- **ruff:** Python linting and code formatting.

## 10. Create the Django Project

```bash
django-admin startproject config .
```

The `config` directory contains the main Django project configuration. The trailing `.` creates the project in the current directory.

## 11. Create the Accounts App

```bash
python manage.py startapp accounts
```

This app can contain user management, registration, authentication, and account-related functionality.

## 12. Run Initial Database Migrations

```bash
python manage.py migrate
```


## 13. Start the Development Server

```bash
python manage.py runserver
```

Open the following URLs:

- **Backend:** http://127.0.0.1:8000/
- **Django Admin:** http://127.0.0.1:8000/admin/

## 14. Save Dependencies

Once the initial setup is complete, save the installed packages:

```bash
pip freeze > requirements.txt
```

To install the same dependencies in another environment:

```bash
pip install -r requirements.txt
```

## Useful Commands

| Purpose | Command |
|---|---|
| Activate environment (Windows CMD) | `venv\Scripts\activate.bat` |
| Start server | `python manage.py runserver` |
| Create migrations | `python manage.py makemigrations` |
| Apply migrations | `python manage.py migrate` |
| Create superuser | `python manage.py createsuperuser` |
| Run tests | `pytest` |
| Check project configuration | `python manage.py check` |
| Lint Python code | `ruff check .` |
| Format Python code | `ruff format .` |
| Save dependencies | `pip freeze > requirements.txt` |

---

**Technology Stack:** Python · Django · Django REST Framework · Simple JWT · PostgreSQL · Gunicorn · pytest · Ruff
