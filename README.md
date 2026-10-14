# Sales & Invoicing Management API

A professional REST API built with **Python, Django REST Framework, and PostgreSQL** for managing items, customers, sales orders, sales order details, invoices, and payment statuses.

## Technology Stack

- **Backend:** Python, Django, Django REST Framework
- **Database:** PostgreSQL
- **Database Driver:** Psycopg 3
- **Authentication:** JWT using Simple JWT
- **API Documentation:** drf-spectacular (Swagger UI / ReDoc)
- **Configuration:** django-environ
- **CORS:** django-cors-headers
- **Testing:** pytest, pytest-django
- **Code Quality:** Ruff
- **Production Server:** Gunicorn

## Project Structure

```text
BasicApp/
├── apps/
│   ├── __init__.py
│   ├── common/
│   │   ├── migrations/
│   │   ├── models.py
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── tests.py
│   │   └── views.py
│   ├── sales/
│   │   ├── migrations/
│   │   ├── models.py
│   │   ├── serializers.py
│   │   ├── views.py
│   │   ├── urls.py
│   │   └── tests.py
│   └── invoicing/
│       ├── migrations/
│       ├── models.py
│       ├── serializers.py
│       ├── views.py
│       ├── urls.py
│       └── tests.py
├── config/
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   └── wsgi.py
├── venv/
├── .env
├── .env.example
├── .gitignore
├── manage.py
├── pytest.ini
├── pyproject.toml
└── requirements.txt
```

> The structure above represents the target organization. Create files such as serializers, URLs, tests, and configuration files as you implement the corresponding features.

## Business Modules

### 1. Common — Master Data

**Item Master**
- Create, retrieve, update, and deactivate items.
- Maintain item codes, names, units of measure, and unit prices.
- Validate unique item codes and non-negative prices.

**Customer Master**
- Create, retrieve, update, and deactivate customers.
- Maintain customer codes, names, phone numbers, email addresses, and addresses.
- Validate unique customer codes.

### 2. Sales — Sales Orders

- Create and manage sales orders.
- Add multiple items to each sales order.
- Validate positive quantities and non-negative unit prices.
- Calculate line amounts and sales order totals.
- Manage sales order statuses:
  - `DRAFT`
  - `CONFIRMED`
  - `INVOICED`
  - `CANCELLED`
- Prevent invalid status transitions and inconsistent order totals.

### 3. Invoicing — Invoice Management

- Generate one invoice per eligible sales order.
- Calculate invoice subtotals, tax amounts, and net amounts.
- Maintain payment statuses:
  - `UNPAID`
  - `PARTIAL`
  - `PAID`
- Prevent duplicate invoices for the same sales order.
- Perform invoice creation and sales status updates within database transactions.

**Tax note:** The initial SQL example uses an 18% tax rate. Implement this as configurable business logic rather than hardcoding it into every endpoint.

## Prerequisites

Install the following before starting:

- Python — a stable release compatible with the selected Django version.
- PostgreSQL — database server.
- Git — version control.
- VS Code — recommended editor.

Official resources:

- Python: https://www.python.org/downloads/
- Django: https://www.djangoproject.com/
- Django REST Framework: https://www.django-rest-framework.org/
- PostgreSQL: https://www.postgresql.org/download/
- Git: https://git-scm.com/downloads

Verify Python and pip:

```bash
python --version
python -m pip --version
```

Verify PostgreSQL connectivity using `psql`:

```bash
psql --version
```

## 1. Navigate to the Project

Open the VS Code terminal and navigate to the directory containing `manage.py`.

```cmd
cd D:\PythonPrototype\Materials\BasicApp
```

All `manage.py` commands must run from this directory unless an alternative path is explicitly supplied.

## 2. Create and Activate the Virtual Environment

Create the virtual environment if it does not already exist:

```cmd
python -m venv venv
```

**Windows Command Prompt:**

```cmd
venv\Scripts\activate.bat
```

**Windows PowerShell:**

```powershell
.\venv\Scripts\Activate.ps1
```

**Linux/macOS:**

```bash
source venv/bin/activate
```

After activation, the terminal should show `(venv)`.

If the environment already exists and is activated, do not recreate it.

## 3. Upgrade pip

```bash
python -m pip install --upgrade pip
```

## 4. Install Backend Dependencies

Install the core framework and API packages:

```bash
pip install django djangorestframework
```

Install JWT authentication and API documentation:

```bash
pip install djangorestframework-simplejwt drf-spectacular
```

Install environment configuration and CORS support:

```bash
pip install django-environ django-cors-headers
```

Install PostgreSQL and production server dependencies:

```bash
pip install "psycopg[binary]" gunicorn
```

Install development and testing dependencies:

```bash
pip install pytest pytest-django ruff
```

Save dependencies:

```bash
pip freeze > requirements.txt
```

For reproducible production builds, pin and test compatible dependency versions rather than relying indefinitely on unbounded latest versions.

## 5. Initialize the Django Project

If the Django project has already been created, skip this step.

```bash
django-admin startproject config .
```

The root directory should contain:

```text
BasicApp/
├── config/
├── venv/
└── manage.py
```

## 6. Create the Business Apps

Your existing `apps/common` directory contains master-data functionality. Keep it and create the sales and invoicing apps.

Create the destination folders if they do not exist:

```cmd
mkdir apps\sales
mkdir apps\invoicing
```

If either folder already exists, do not run its `mkdir` command again.

Create the apps:

```bash
python manage.py startapp sales apps/sales
python manage.py startapp invoicing apps/invoicing
```

Ensure `apps/__init__.py` exists so Python recognizes the parent package.

Configure the app paths in their respective `apps.py` files. For example:

```python
from django.apps import AppConfig


class SalesConfig(AppConfig):
    default_auto_field = "django.db.models.BigAutoField"
    name = "apps.sales"
```

Use `"apps.common"` for the common app and `"apps.invoicing"` for the invoicing app.

## 7. Configure Installed Apps

Open `config/settings.py` and register the required applications.

```python
INSTALLED_APPS = [
    # Django applications
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",

    # Third-party applications
    "rest_framework",
    "rest_framework_simplejwt",
    "drf_spectacular",
    "corsheaders",

    # Business applications
    "apps.common",
    "apps.sales",
    "apps.invoicing",
]
```

Add `"corsheaders.middleware.CorsMiddleware"` near the top of `MIDDLEWARE`, before `CommonMiddleware`.

Configure DRF, JWT, and API documentation settings as the corresponding features are implemented. Installing these packages alone does not enable authentication or Swagger documentation.

## 8. Create the PostgreSQL Database

Open PostgreSQL using `psql`:

```bash
psql -U postgres -h localhost -p 5432
```

Create the application database:

```sql
CREATE DATABASE sales_db;
```

Exit `psql`:

```sql
\q
```

If the database already exists, do not create it again.

## 9. Configure Environment Variables

Create a `.env` file in the project root, alongside `manage.py`.

```dotenv
DJANGO_SECRET_KEY=replace-with-a-unique-secret
DJANGO_DEBUG=True
DJANGO_ALLOWED_HOSTS=localhost,127.0.0.1

DB_NAME=sales_db
DB_USER=postgres
DB_PASSWORD=your_actual_postgresql_password
DB_HOST=localhost
DB_PORT=5432
```

Replace the database password and secret key with your actual values.

Create `.env.example` with placeholder values for other developers. Add `.env` and `venv/` to `.gitignore`.

Never commit real secrets or database credentials to Git.

## 10. Configure PostgreSQL in Django

Configure the database in `config/settings.py` using environment variables.

```python
import environ

env = environ.Env()

environ.Env.read_env()

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": env("DB_NAME"),
        "USER": env("DB_USER"),
        "PASSWORD": env("DB_PASSWORD"),
        "HOST": env("DB_HOST", default="localhost"),
        "PORT": env("DB_PORT", default="5432"),
    }
}
```

Ensure that the PostgreSQL server is running and the database credentials are correct.

## 11. Implement the Database Models

Implement models corresponding to the existing business schema.

| Django model | PostgreSQL table |
|---|---|
| `Item` | `item_master` |
| `Customer` | `customer_master` |
| `SalesOrder` | `sales_header` |
| `SalesOrderLine` | `sales_details` |
| `Invoice` | `invoice` |

Preserve the database table names, primary keys, foreign keys, unique constraints, check constraints, decimal precision, and generated columns.

Use Django's `DecimalField` for monetary values. Do not use Python floating-point numbers for financial calculations.

**Important:** Decide whether Django will create the schema or manage an existing schema created by your SQL script. Do not run initial migrations blindly against existing tables. Reconcile the schema and migration history first.

## 12. Create and Apply Migrations

For a new database managed by Django:

```bash
python manage.py check
python manage.py makemigrations common sales invoicing
python manage.py migrate
```

Django migrations should be committed to Git so schema changes can be reproduced in other environments.

For an existing database created by your SQL script, inspect the schema before choosing an appropriate migration baseline.

## 13. Create a Superuser

```bash
python manage.py createsuperuser
```

Use the generated account to access the Django admin after registering the appropriate models.

## 14. Run the Development Server

```bash
python manage.py runserver
```

Development URLs:

- Backend: http://127.0.0.1:8000/
- Django Admin: http://127.0.0.1:8000/admin/

The API endpoints and Swagger/ReDoc URLs become available after the corresponding URL patterns and views have been implemented.

## 15. API Development Guidelines

Use a consistent API structure:

```text
/api/v1/items/
/api/v1/customers/
/api/v1/sales-orders/
/api/v1/invoices/
```

Recommended implementation order:

1. Item and customer CRUD endpoints.
2. Sales order creation and detail-line management.
3. Transactional sales total calculation.
4. Invoice generation and tax calculation.
5. JWT authentication and permission enforcement.
6. API pagination, filtering, and validation.
7. Swagger/ReDoc documentation.
8. Automated tests and production deployment configuration.

Use serializers for request validation, views or viewsets for HTTP handling, and service functions for complex business operations.

## 16. Testing and Code Quality

Run Django's configuration checks:

```bash
python manage.py check
```

Run automated tests:

```bash
pytest
```

Check code quality:

```bash
ruff check .
```

Format the code:

```bash
ruff format .
```

Tests should cover duplicate codes, invalid quantities, negative prices, sales totals, foreign-key integrity, duplicate invoices, valid status transitions, tax calculations, and permission enforcement.

## 17. Save and Restore Dependencies

Save the installed dependency versions:

```bash
pip freeze > requirements.txt
```

Install dependencies in another environment:

```bash
python -m pip install -r requirements.txt
```

For team development and deployment, use a controlled dependency-management strategy and test installations in a clean environment.

## 18. Production Deployment Checklist

Before deploying:

- Set `DEBUG=False`.
- Use a unique production secret key supplied through environment variables.
- Configure production allowed hosts and HTTPS.
- Restrict CORS to trusted frontend origins.
- Configure production database credentials and backups.
- Run migrations as a controlled deployment step.
- Use Gunicorn or an appropriate production server.
- Configure structured logging and error monitoring.
- Run `python manage.py check --deploy`.
- Ensure database permissions and API permissions follow least privilege.

## Useful Commands

| Purpose | Command |
|---|---|
| Activate environment (Windows CMD) | `venv\Scripts\activate.bat` |
| Start development server | `python manage.py runserver` |
| Create migrations | `python manage.py makemigrations` |
| Apply migrations | `python manage.py migrate` |
| Create admin account | `python manage.py createsuperuser` |
| Check configuration | `python manage.py check` |
| Run tests | `pytest` |
| Lint code | `ruff check .` |
| Format code | `ruff format .` |
| Save dependencies | `pip freeze > requirements.txt` |

---

**Project:** Sales & Invoicing Management API

**Stack:** Python · Django · Django REST Framework · PostgreSQL · JWT · OpenAPI · pytest · Ruff
