# Django Interview Questions & Practice Guide

This guids contains **50 frequently asked Django inetrview questions + Practice coding assessment problems** (this question asked in Deloitte, JPMorgan, McKinsey, and others.)

These assessment are based on **my personal interview** this companies which provide practice question from  HackerRank, CodeChef, etc.

Use this to:
✅ Revise Django concepts quickly
✅ Practice real-world coding challenges in under 1 hour
✅ Boost your chances of cracking Django interviews


# Django Introduction
<details>
<summary>1. What is Django, and what are its key features?</summary>

---
Django is a **high-level Python web framework** that enables rapid development of secure and scalable web applications.
Key Features:
- **MVT architecture** (Model-View-Template).
- **Built-in ORM** (works with multiple databases like PostgreSQL, MySQL, SQLite).
- **Authentication system** (users, sessions, groups, permissions).
- **URL routing system** (clean and readable URLs).
- **Template engine** for dynamic HTML rendering.
- **Security** (CSRF protection, SQL injection prevention, XSS protection).
- **Admin interface** (auto-generated from models).
- **Scalability & pluggable apps** (reusable apps for rapid development).
</details>

<details>
<summary>2. Explain the MVT (Model-View-Template) architecture in Django</summary>

MVT is Django’s architectural pattern (similar to MVC, but with differences).
- **Model:** Defines the database schema (tables, relationships). Example: `models.py`.
- **View:** Contains business logic. Takes request, interacts with model, returns response. Example: `views.py`.
- **Template:** Handles UI/HTML presentation. Example: `templates/index.html`.
</details>


<details>
<summary>3. How is Django different from Flask or FastAPI?</summary>

---

| Feature       | **Django** (Full-stack) | **Flask** (Micro)            | **FastAPI** (Async, modern)          |
| ------------- | ----------------------- | ---------------------------- | ------------------------------------ |
| Type          | Full-stack framework    | Micro framework              | API-focused (async)                  |
| ORM           | Built-in ORM            | No ORM (SQLAlchemy optional) | No ORM built-in                      |
| Admin Panel   | Yes (auto-generated)    | No                           | No                                   |
| Performance   | Good, but heavier       | Lightweight                  | Faster (async, Starlette)            |
| Best Use Case | Large apps, monoliths   | Small apps, flexibility      | High-performance APIs, microservices |

</details>

<details>
<summary>4. What is the role of manage.py?</summary>

---

`manage.py` is a command-line utility for Django projects.

**Roles**
Run development server → `python manage.py runserver`.
Create new apps → `python manage.py startapp appname`.
Apply migrations → `python manage.py migrate`.
Create superuser → `python manage.py createsuperuser`.
Run tests → `python manage.py test`.

👉 It works as a wrapper around django-admin with project-specific settings.
</details>

<details>
<summary>5. Difference between project and app in Django.</summary>

---

**Project:** The overall Django web application (container). It has settings, configurations, and can contain multiple apps. Example: mysite/.
**App:** A module that handles one specific functionality (reusable). Example: blog app, authentication app, payment app.
👉 A project = many apps. An app = can be reused across projects.
</details>



<details>
<summary>6. What are Django settings, and how can you handle multiple environments (dev, prod)?</summary>

---

`settings.py` contains configuration for the Django project (DB configs, middleware, installed apps, static files, etc.).

Handling multiple environments:
1. Separate settings files
- `settings/base.py`, `settings/dev.py`, `settings/prod.py`.
- Import base, override environment-specific values.
2. Environment variables
- Use python-decouple or django-environ.
- Example: DATABASE_URL, DEBUG, SECRET_KEY.
</details>