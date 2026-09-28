# My Book Library

My Book Library — a Django database-driven book listing and add-book app.

## What It Does

- Lists books on a public page.
- Adds books through a browser form.
- Lets an administrator manage books through Django admin.

## Tech Stack

- Python 3.14.3
- Django 6.1.1
- SQLite (`db.sqlite3`)

## Folder Structure

```text
practice2_library/
├── manage.py
├── db.sqlite3
├── .gitignore
├── .venv/
├── config/
│   ├── settings.py
│   └── urls.py
└── books/
    ├── admin.py
    ├── forms.py
    ├── models.py
    ├── urls.py
    ├── views.py
    ├── migrations/
    └── templates/books/
        ├── add_book.html
        └── book_list.html
```

## Setup

From the directory containing the project:

```powershell
cd practice2_library
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install Django==6.1.1
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

The sample database is kept in `db.sqlite3`, so the existing sample data can be backed up with the project.

## URLs

- `/` — public book list
- `/add/` — add-book form
- `/admin/` — Django administration site

## Course Context

This project was built as Lab Exercise 8 for a college Django lab exercise.
