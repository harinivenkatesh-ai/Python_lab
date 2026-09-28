# How My Book Library Was Built

This document explains the project in the order it was developed. It is written as a study guide for a live Django demonstration.

## Phase 1-2: Workspace, Virtual Environment, Project, and App

The project root is `practice2_library`. A Python virtual environment named `.venv` keeps the project's packages separate from the system Python installation. On Windows PowerShell, it can be activated with:

```powershell
.\.venv\Scripts\Activate.ps1
```

Django was installed into the environment. The Django project was created with:

```powershell
django-admin startproject config .
```

The dot means that Django places `manage.py` and the `config` package directly in the current folder instead of creating another nested project folder.

The `books` application was created with:

```powershell
python manage.py startapp books
```

A Django project contains the overall configuration, while an app contains one area of functionality. The `books` app was added to `INSTALLED_APPS` in `config/settings.py`. This registration tells Django to load the app, discover its models and migrations, and include it in Django's application registry.

## Phase 3-4: Model and Migrations

The main data object is the `Book` model in `books/models.py`:

- `title` is a `CharField` with a maximum length of 200 characters.
- `author` is a `CharField` with a maximum length of 100 characters.
- `year_published` is an `IntegerField` for the publication year.
- `is_available` is a `BooleanField` that stores either true or false and defaults to true.
- `__str__` returns the title, so books have a readable name in the admin site and other Django displays.

Django uses two separate migration commands because they do different jobs:

```powershell
python manage.py makemigrations
python manage.py migrate
```

`makemigrations` compares the model definition with the migration history and writes a migration file describing the database change. It does not change the database yet. `migrate` reads the migration files and applies those changes to the database. Separating these steps lets the changes be reviewed and tracked before they are applied.

The resulting SQLite database is `db.sqlite3` in the project root.

## Phase 5: Admin Site

Django's admin site is a ready-made management interface for staff users. A superuser was created with:

```powershell
python manage.py createsuperuser
```

In `books/admin.py`, the `Book` model is imported and registered with `admin.site.register`. The `BookAdmin` class sets `list_display`, which tells the admin list page to show the title, author, publication year, and availability status as columns.

After logging in at `/admin/`, an administrator can add, edit, and delete books without a separate custom management page.

## Phase 6: Public Listing Page

The public listing follows Django's normal URL, view, and template flow:

1. `config/urls.py` includes the URL patterns from `books.urls` at the root path.
2. `books/urls.py` maps the empty path to `views.book_list` and gives it the name `book_list`.
3. `book_list` in `books/views.py` calls `Book.objects.all()` to request all book records from the database. It passes the resulting queryset to the template in a context dictionary under the key `books`.
4. `books/templates/books/book_list.html` displays the data.

`Book.objects.all()` is a Django ORM query. It represents all rows in the `Book` database table. Django evaluates the query when the data is needed, then gives the template the book objects to display.

The template uses a Django `{% for book in books %}` loop. Each loop iteration represents one book. `forloop.counter` gives the display number starting at 1. The template prints each book's fields and uses the `yesno` filter to show `Available` when `is_available` is true and `Issued` when it is false. The `{% empty %}` block displays `No books yet.` when there are no records.

## Phase 7: Add Book Form

`books/forms.py` defines `BookForm` as a `ModelForm`. Its `Meta` class connects the form to the `Book` model and selects the fields that users may enter.

The `add_book` view handles two kinds of request:

- On a GET request, it creates an empty `BookForm` and renders the form page.
- On a POST request, it builds a form from `request.POST`, which contains the submitted values.

`form.is_valid()` checks the submitted data against the model field rules, including required fields, text lengths, and valid numeric input. If the data is valid, `form.save()` creates the new `Book` row in `db.sqlite3`.

After saving, the view redirects to the named `book_list` URL. Redirecting after a successful POST prevents the browser from accidentally submitting the same form again when the page is refreshed.

The form template includes `{% csrf_token %}`. This adds a secret token tied to the user's session. Django checks the token on POST requests so another website cannot silently submit a form using the user's browser session. This protects the form against cross-site request forgery.

The route `add/` maps to the `add_book` view, and the listing page links to it with `{% url 'add_book' %}`. The form page links back with `{% url 'book_list' %}`.

## Purpose of Each File

| File or folder | Purpose |
|---|---|
| `books/models.py` | Defines the `Book` data model and its database fields. |
| `books/views.py` | Contains the listing and add-book request handling. |
| `books/urls.py` | Maps the books app URLs to view functions. |
| `books/admin.py` | Registers `Book` and configures its admin list display. |
| `books/forms.py` | Defines the model-backed form for adding books. |
| `config/settings.py` | Stores project settings, installed apps, middleware, templates, and database configuration. |
| `manage.py` | Runs Django management commands for this project. |
| `books/templates/` | Contains the HTML templates rendered by the views. |
| `db.sqlite3` | Stores the project's database tables, users, migrations, and sample book data. |
