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

```
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

```commandline
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

### Django ORM (Object relational mapper)

👉 It allows interaction with the database using Python objects instead of SQL. Avoids writing raw SQL.

#### 1. `select_related`: 
Used with one-to-one and many-to-one(ForeignKey) relations

**e.g:**
```
# models.py
class Author(models.Model):
    name = models.CharField(max_length=100)

class Post(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```

**Without Optimization (N+1 Problem)**
```
posts = Post.objects.all()
for post in posts:
    print(post.author.name)   # ❌ One query for posts + extra query for each author
```
`Query 1: SELECT * FROM post;`
`Query 2..N: SELECT * FROM author WHERE id=?;`
**👉 If you have 100 posts → 101 queries!**

**With select_related**
```
posts = Post.objects.select_related("author").all()
for post in posts:
    print(post.author.name)   # ✅ Only 1 query (JOIN with Author)
```
`SELECT post.*, author.* FROM post INNER JOIN author ON post.author_id = author.id;`

#### 2. `prefetech_related`: 
Used with one-to-many and many-to-many relations

**e.g:** 
```
# models.py
class Course(models.Model):
    name = models.CharField(max_length=100)

class Student(models.Model):
    name = models.CharField(max_length=100)
    courses = models.ManyToManyField(Course, related_name="students")
```

**Without Optimization**
```
students = Student.objects.all()
for student in students:
    print([c.name for c in student.courses.all()])  # ❌ Extra query for each student
```
`Query 1: SELECT * FROM student;`
`Query 2..N: SELECT * FROM course ...`
**👉 If you have 50 students → 51 queries!**

**With prefetch_related**
```
students = Student.objects.prefetch_related("courses").all()
for student in students:
    print([c.name for c in student.courses.all()])  # ✅ Only 2 queries
```
`Query 1: SELECT * FROM student;`
`Query 2: SELECT * FROM course INNER JOIN student_courses;`

