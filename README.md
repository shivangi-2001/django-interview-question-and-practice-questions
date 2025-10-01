# Django Interview Questions & Practice Guide

This guids contains **50 frequently asked Django inetrview questions + Practice coding assessment problems** (this question asked in Deloitte, JPMorgan, McKinsey, and others.)

These assessment are based on **my personal interview** this companies which provide practice question from  HackerRank, CodeChef, etc.

Use this to:
✅ Revise Django concepts quickly
✅ Practice real-world coding challenges in under 1 hour
✅ Boost your chances of cracking Django interviews


## Quick Recap

**Django** is high-level python web framework that enable rapid development and clean, pragmatic design.

It also reffered as the **"batteries-included"** because it comes with a lot of built in features: `ORM`, `authentication`, `admin panel`, `forms`, `middleware`, `template engine` etc which reducing the need for the third party libraries and make Python web development faster, modular, and production-ready.

Django **MVT (Model, View, Template) architecture**:
***Model***: Define the data struture or (database schema).It is like making SQL `create Table` `table Schema`.
***View***: Contain the business logic, acts as bridge between Model and Template
***Template***: Handles presentation layer (User interface) HTML pages.

> Difference between **MVC (Model, View, Constrollers)** & **MVT (Model, View, Template)**: Model are same in both, views in MVC is presentation layer and Controllers are the business logic for render and computing cost.

Django Folder structure:
**Django Project:** The overall web application, conatining settings, configurations, and multiple apps.
`django-admin startproject ${project_name}`

**Django Folder:** A modular component of a project of a project that handles a specifc functionality `django-admin startapp {app_name}`

```shell
my_project/
│
├── manage.py
├── README.md
├── requirements.txt
├── .gitignore
├── .env
├── my_project/             # Main project folder
│   ├── __init__.py
│   ├── settings.py         # Or a settings/ folder with separate files (base.py, dev.py, prod.py)
│   ├── urls.py
│   ├── wsgi.py
│   ├── asgi.py
│   └── static/             # Global static files (optional, can be managed by each app)
│
├── apps/                   # Directory for all Django apps
│   ├── __init__.py         # Makes 'apps' a Python package
│   ├── app1/               # First Django app
│   │   ├── migrations/
│   │   ├── __init__.py
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── models.py
│   │   ├── tests.py
│   │   ├── views.py
│   │   ├── urls.py         # App-specific URL configuration
│   │   ├── static/         # App-specific static files
│   │   └── templates/      # App-specific templates
│   └── app2/               # Another Django app
│       ├── migrations/
│       ├── __init__.py
│       ├── admin.py
|       |-- ......
│       ├── static/
│       └── templates/
│
├── templates/              # Global templates
│   ├── base.html
│   └── ...
│
└── static/                 # Global static files (e.g., CSS, JavaScript, images)
    ├── css/
    ├── js/
    └── images/
```

**Important Files:**
`settings.py` store all configuration (database, middleware, installed apps, static files etc).

`manage.py` Is a command-line utility that helps interact with the Django project.

```shell
# Run development server
python manage.py runserver

# Create new database migrations based on changes detected in your models.
python manage.py makemigrations

# Applies database migrations, creating or updating database tables.
python manage.py migrate

# Opens a Python shell where you can interact with your Django project's models and other components.
python manage.py shell

# Run tests for django
python mange.py test [app_name]

# Create a superuser (for admin panel)
python manage.py createsuperuser
```

## Django ORM (Object relational mapper)

👉 It allows interaction with the database using Python objects instead of SQL. Avoids writing raw SQL.

**Detials Django ORM Cheatsheet** [📄 CheatSheet for ORM](django-orm-cheatsheet.md)

|| Example | Where to use |
|---| ---|---|
| `select_related` | **Book.objects.select_related('Author').all()** #one author can have multiple books | Many-to-One & one-to-one relation |
| `prefetch_related` | **Student.objects.prefetch_related("courses").all()** # single student can take multiple courses or multiple course taken by multiple students | one-to-many and many-to-many relations |
| `Avg()` | **Employee.objects.aaggregate(avg_salary=Avg('salary'))** #Calculate the average salary of Employees | |
| `Count()` | **Author.objects.annotate(num_books=Count("book"))** #return the authors with books count ||

---

## Django Views

Two types of views functional based and class based view **(from django.views import View)**

#### Function based views
```python
from django.http import HttpResponse

def hello(request):
    return HttpResponse("Hello World")
```

#### Class based views
Define methods like get(), post(), put(), delete() to handle HTTP methods.
```python
from django.views import View
from django.http import HttpResponse

class HelloView(View):
    def get(self, request):
        return HttpResponse("GET Request")

    def post(self, request):
        return HttpResponse("POST Request")
```

#### Mixins based views
- Small, reusable classes that add extra behavior to CBVs
- Used with inheritance to extend functionality without rewriting logic.
```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import DetailView
from .models import Book
# `LoginRequiredMixin`only loggin user can access
class BookDetailView(LoginRequiredMixin, DetailView):
    model = Book
    template_name = "book_detail.html"
```

#### Generic Class-Based Views (GCBVs)

Pre-built CBVs for common patterns (CRUD) so you don’t need to write boilerplate.
```python
from django.views.generic import ListView, DetailView, CreateView, UpdateView, DeleteView
from .models import Book

class BookListView(ListView):
    model = Book
    template_name = "books.html"

class BookDetailView(DetailView):
    model = Book
    template_name = "book_detail.html"

class BookCreateView(CreateView):
    model = Book
    fields = ["title", "published_year"]
    template_name = "book_form.html"
    success_url = "/books/"
```

---

## Django Forms
Form is a Django class that handles: Rendering HTML form fields, Validating user input, Cleaning (normalizing) data and Converting it into Python objects.

📍 Implementation using `forms.Form`
```python
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)
```
How to use in the views.py
```python
from django.shortcuts import render
from .forms import ContactForm

def contact_view(request):
    if request.method == "POST":
        form = ContactForm(request.POST)
        if form.is_valid():
            print(form.cleaned_data)  # safe, validated data
    else:
        form = ContactForm()
    return render(request, "contact.html", {"form": form})
```
📍 Implementation using `forms.ModelForm`

```python
from django import forms
from .models import Book

class BookForm(forms.ModelForm):
    class Meta:
        model = Book
        fields = ["title", "published_year"]
```

---

## Django Signals

**Signals** in Django are a way for different parts of an application to **communicate with each other** when certain actions occur.
They work like event listeners:
- An event (signal) happens (e.g., object saved, deleted, user logged in).
- A receiver (function) gets notified and performs some action.

This helps you **decouple code →** the sender (event) doesn’t need to know what receivers will do.

### How Signals Work
- Sender → The model or event that triggers the signal.
- Receiver → A function that listens for the signal and executes code.
- Dispatcher → Django’s internal mechanism (Signal class) that connects senders and receivers.

#### Built-in Django Signals
1. **Model Signals**
    `pre_save`, `post_save` → before/after saving an object.
    `pre_delete`, `post_delete` → before/after deleting an object.
    `m2m_changed` → when a ManyToMany relation changes.
2. **Request/Response Signals**
    `request_started`, `request_finished`.
3. **Authentication Signals**
    `user_logged_in`, `user_logged_out`, `user_login_failed`.