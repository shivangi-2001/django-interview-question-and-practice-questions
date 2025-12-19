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



<details>
<summary>7. What are Django signals? Give examples of when to use them.</summary>

---
- **Signals** are a way for Django apps to communicate and respond to events.
- Example: When a User is created, you may want to automatically create a Profile.

**Example usage:**
```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User
from .models import Profile

@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)
```
</details>

<details>
<summary>8. How does Django handle requests internally (request-response cycle)?</summary>

---
1. **Request hits WSGI/ASGI server** (Gunicorn, Daphne, uWSGI).
2. **Django URL resolver** (urls.py) matches the request to a view.
3. **Middleware processing** (authentication, security, sessions, etc.).
4. **View executes** → interacts with Models/ORM → fetches data.
5. **Template rendering** (if HTML response).
6. **Response generated** (HTML, JSON, API response).
7. **Middleware** post-processing.
8. **Final HTTP Response** returned to client.
👉 In short: Request → Middleware → URL → View → Model → Template → Response.
</details>


# Models & ORM 


<details>
<summary>1. How does Django ORM work?</summary>

---
- Django ORM (Object Relational Mapper) allows developers to interact with databases using Python objects instead of raw SQL.
- Each Django Model = Database Table.
- Each Model instance = Row in table.
- ORM translates Python queries (`Model.objects.filter()`) → SQL → returns Python objects.

</details>

<details>
<summary>2. Explain ForeignKey, OneToOneField, and ManyToManyField.</summary>

- **ForeignKey** → `One-to-Many relation`. Example: Each Post belongs to one User.
- **OneToOneField** → `One-to-One relation`. Example: Each User has exactly one Profile.
- **ManyToManyField** → `Many-to-Many relation`. Example: Students enrolled in many Courses.


```python
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)   # 1-1
    bio = models.TextField()

class Post(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)      # 1-M
    content = models.TextField()

class Course(models.Model):
    students = models.ManyToManyField(User)                       # M-M
```
</details>

<details>
<summary>3. What are model managers?</summary>
Managers are interfaces through which ORM queries are made.
By default, every model has objects manager.
You can create custom managers to add extra query methods.

```python
class EmployeeManager(models.Manager):
    def active(self):
        return self.filter(is_active=True)

class Employee(models.Model):
    name = models.CharField(max_length=100)
    is_active = models.BooleanField(default=True)

    objects = EmployeeManager()

# Usage
Employee.objects.active()
```
</details>


<details>
<summary>4. How do you perform raw SQL queries in Django?</summary>

---
- Use raw() for SELECT queries.
- Use connection.cursor() for custom SQL execution.

```python
# Raw query
employees = Employee.objects.raw('SELECT * FROM employee WHERE age > %s', [30])

# Using cursor
from django.db import connection
with connection.cursor() as cursor:
    cursor.execute("SELECT COUNT(*) FROM employee")
    row = cursor.fetchone()
```
</details>


<details>
<summary>5. Difference between select_related() and prefetch_related().</summary>

---
| Feature     | `select_related()`                 | `prefetch_related()`                     |
| ----------- | ---------------------------------- | ---------------------------------------- |
| Works on    | **ForeignKey**, **OneToOne**       | **ManyToMany**, reverse ForeignKey       |
| DB queries  | Joins in **single query**          | Executes **two queries**, caches results |
| Performance | Faster for one-to-one/foreign keys | Better for many-to-many                  |

```python
# Avoid N+1 queries
# select_related (JOIN)
posts = Post.objects.select_related('user').all()

# prefetch_related (multiple queries)
courses = Course.objects.prefetch_related('students').all()
```
</details>



<details>
<summary>6. How do you optimize ORM queries in Django?</summary>

---

- Use `select_related()` and `prefetch_related()` to avoid N+1 queries.
- Use `values()` / `values_list()` if you need only certain fields.
- Use `defer()`/`only()` to fetch limited columns.
- Add indexes in models `(db_index=True)`.
- Use `Q` objects for complex queries instead of chaining filters.
- Cache expensive queries (Redis, Memcached).
</details>


<details>
<summary>7. Explain database transactions in Django?</summary>

---

Django supports **atomic transactions** → all queries inside either succeed or fail.
Use `transaction.atomic()` for atomic blocks.
```python
from django.db import transaction

with transaction.atomic():
    order = Order.objects.create(user=user)
    Payment.objects.create(order=order, amount=100)
    # If Payment fails → Order will rollback automatically
```
</details>



<details>
<summary>8. How to implement soft deletes in Django?</summary>

---

- By default, delete() removes row from DB.
- Soft delete = mark object as inactive instead of deleting.
```python
class Employee(models.Model):
    name = models.CharField(max_length=100)
    is_deleted = models.BooleanField(default=False)

    def delete(self):
        self.is_deleted = True
        self.save()

# Usage
emp = Employee.objects.get(id=1)
emp.delete()   # Will just mark deleted
```

* For full projects, use libraries like django-softdelete or django-safedelete.

</details>


# Views & URLs
<details>
<summary>1. Difference between Function-Based Views (FBV) and Class-Based Views (CBV)?</summary>
<br/>

- **Views** are Python functions or classes that handle requests and return responses (HTML, JSON, XML, etc.).
- Views = Business logic of your app.

```python
# function based view
from django.http import HttpResponse

def home(request):
    return HttpResponse("Hello, Django!")

# class based view
from django.views import View
from django.http import HttpResponse

class HomeView(View):
    def get(self, request):
        return HttpResponse("Hello from CBV!")
```

| Feature       | FBV (Function)       | CBV (Class)              |
| ------------- | -------------------- | ------------------------ |
| Simplicity    | Easy to read/write   | Slightly more complex    |
| Reusability   | Less reusable        | Highly reusable          |
| Customization | Use decorators       | Use mixins & inheritance |
| Example       | `def view(request):` | `class View(View):`      |

</details>



<details>
<summary>2. Explain Django’s generic class-based views (ListView, DetailView, etc.)?</summary>
</br>

Django provides **pre-built views** for common tasks → less code.
- `ListView` (list of objects)
- `DetailView` (single object details)
- `CreateView`, `UpdateView`, `DeleteView`
👉 Example:
from django.views.generic import ListView
from .models import Post

class PostListView(ListView):
    model = Post
    template_name = 'posts.html'
</details>

<details>
<summary>3. What are mixins in Django CBVs? Give an example?</summary>
<br/>

Mixins = Reusable classes that add functionality to CBVs.
Example:`LoginRequiredMixin` forces authentication.
```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import DetailView
from .models import Profile

class ProfileView(LoginRequiredMixin, DetailView):
    model = Profile
    template_name = 'profile.html'
```
</details>

<details>
<summary>4. How do you implement REST APIs in Django?</summary>
<br/>

- Create a Django project: `django-admin startproject myproject`
- Create a Django app within your project: `python manage.py startapp api`
- Install DRF using pip: `pip install djangorestframework`
- Add `'rest_framework'` and your app name (e.g., 'api') to `INSTALLED_APPS` in your project's `settings.py`.
- In your app's `models.py`, define the Django models that represent your data.
- **Serializers** convert complex data types (like Django model instances) into native Python data types that can be easily rendered into JSON, XML, or other content types. 
- Create a `serializers.py` file in your app and define serializers for your models using `rest_framework.serializers.ModelSerializer`.
- **Generic Views:** Use classes like `ListCreateAPIView`, `RetrieveUpdateDestroyAPIView` for common CRUD operations.
**ViewSets:** Combine the logic for multiple related views (list, detail, create, update, delete) into a single class.
- Map `URLs` to your views or viewsets. DRF's routers simplify URL configuration for viewsets.
- Create a `urls.py` file in your app.
</details>

<details>
<summary>5. What is the difference between path() and re_path()?</summary>
<br/>

Django uses ***URL dispatcher*** `(urls.py)` to map paths to views.
Uses `path()` or `re_path()`.

```python
from django.urls import path
from .views import home, HomeView

urlpatterns = [
    path('', home, name='home'),                # Function view
    path('class/', HomeView.as_view(), name='cbv_home')  # Class view
]
```
</details>

# Templates
<details>
<summary>1. How does the Django template engine work?</summary>
<br/>

* **Templates** = HTML files with Django template language (DTL) for dynamic content.
* Combine data from views with HTML presentation.
```html
<h1>Welcome {{ user.username }}</h1>
<ul>
  {% for post in posts %}
    <li>{{ post.title }}</li>
  {% endfor %}
</ul>
```
</details>

<details>
<summary>2. What are template tags and filters?</summary>
<br/>

**Tags** → Logic in templates. ({% for %}, {% if %}, {% extends %}).
**Filters** → Modify variables. ({{ name|upper }}, {{ date|date:"Y-m-d" }}).

```html
{% if user.is_authenticated %}
  Hello, {{ user.username|upper }}
{% else %}
  Please login
{% endif %}
```
</details>

<details>
<summary>3. How to prevent XSS in Django templates?</summary>
<br/>

- Django **auto-escapes variables** in templates.
- Example: If `user_input = "<script>alert('XSS')</script>"` → Django will render as text, not HTML.
- To render raw HTML, use `|safe`.

```
{{ user_input }}   <!-- Safe (escaped) -->
{{ user_input|safe }}   <!-- Dangerous -->
```
</details>


<details>
<summary>4. How do you extend templates in Django?</summary>
<br/>

- Use {% extends %} and {% block %} to avoid duplication.
```html
<!-- base html -->
<html>
  <head><title>{% block title %}My Site{% endblock %}</title></head>
  <body>
    <div class="content">{% block content %}{% endblock %}</div>
  </body>
</html>

<!-- home html -->
{% extends "base.html" %}
{% block title %}Home{% endblock %}
{% block content %}
  <h1>Welcome Home!</h1>
{% endblock %}
```
</details>


# Forms & Validation

<details>
<summary>1. Difference between forms.Form and forms.ModelForm.</summary>
<br/>
* `forms.Form` → Create form fields manually (not linked to DB).
* `forms.ModelForm` → Auto-generates fields from a Django Model.

```python
# Form
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()

# ModelForm
from .models import Student
class StudentForm(forms.ModelForm):
    class Meta:
        model = Student
        fields = ['name', 'age']
```
</details>

<details>
<summary>2. How does Django handle form validation?</summary>
<br/>

* Validation happens when you call form.is_valid().
* Steps:
    - Check field validators.
    - Run `clean_<fieldname>()` methods if defined.
    - Run `clean()` method for cross-field validation.
    - Store errors in `form.errors`.
</details>

<details>
<summary>3. How does is_valid() work internally?</summary>
<br/>

* `is_valid()` → Calls `full_clean()` → runs field + custom validations.
* Returns `True` if no errors, else `False`.

```python
form = StudentForm(request.POST)
if form.is_valid():
    form.save()
else:
    print(form.errors)
```
</details>

<details>
<summary>4. How do you handle custom form validations?</summary>
<br/>

* Use `clean_<fieldname>()` or `clean()` method.
```python
class StudentForm(forms.ModelForm):
    class Meta:
        model = Student
        fields = ['name', 'age']

    def clean_age(self):
        age = self.cleaned_data.get('age')
        if age < 18:
            raise forms.ValidationError("Age must be at least 18")
        return age
```
</details>

<details>
<summary>5. How to handle file uploads in Django?</summary>
<br/>

* Use `FileField` or `ImageField` in forms.
* Configure `MEDIA_URL` and `MEDIA_ROOT`.

**MEDIA_ROOT:**

- `MEDIA_ROOT` is an absolute path to the directory where Django will store user-uploaded files on the local filesystem.
- This is the physical location on your server where files like images, videos, or documents uploaded by users through your application will be saved.


**MEDIA_URL:**

- `MEDIA_URL` is the URL prefix that will be used to access the media files stored in MEDIA_ROOT.
- It defines the public-facing URL path that web browsers will use to request and display these media files.

```python
class UploadForm(forms.Form):
    file = forms.FileField()

def upload_file(request):
    if request.method == 'POST':
        form = UploadForm(request.POST, request.FILES)
        if form.is_valid():
            handle_uploaded_file(request.FILES['file'])
```
</details>

# Django Rest Framework (DRF)


<details>
<summary>1. What is Django Rest Framework (DRF), and why is it used?</summary>
<br/>

- DRF = Django REST Framework.
- It makes building REST APIs easy with serialization, authentication, pagination, etc.
</details>

<details>
<summary>2. Difference between Serializer and ModelSerializer.</summary>
<br/>

- **Serializer →** Manually define fields.
- **ModelSerializer →** Auto-generates fields from Django model.
```python
# Normal Serializer
class UserSerializer(serializers.Serializer):
    username = serializers.CharField()
    email = serializers.EmailField()

# ModelSerializer
class UserModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email']
```
</details>

<details>
<summary>3. What are ViewSets and Routers in DRF?</summary>
<br/>

- **ViewSet** = Combine CRUD operations in a single class.
- **Router** = Auto-generates URLs for ViewSets.
```python
from rest_framework import viewsets
from .models import Post
from .serializers import PostSerializer

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer

# urls.py
from rest_framework.routers import DefaultRouter
router = DefaultRouter()
router.register('posts', PostViewSet)
urlpatterns = router.urls
```
</details>

<details>
<summary>4. Explain throttling, versioning, and pagination in DRF.</summary>

<br/>
- **Throttling** → Limit request rate (e.g., 100 requests/hour).
- **Versioning** → Manage different API versions (v1/posts, v2/posts).
- **Pagination** → Return results in pages (PageNumber, LimitOffset, Cursor).
<br/>

</details>

<details>
<summary>5. How do you implement authentication in DRF (Token, JWT, OAuth2)?</summary>
<br/>

* Built-in: SessionAuth, TokenAuth.
* Common: JWT (via `djangorestframework-simplejwt`).
* OAuth2 (via `django-oauth-toolkit`).

```python
INSTALLED_APPS += ['rest_framework_simplejwt']
REST_FRAMEWORK = {
  'DEFAULT_AUTHENTICATION_CLASSES': [
    'rest_framework_simplejwt.authentication.JWTAuthentication'
  ]
}
```
</details>

<details>
<summary>6. Difference between SessionAuth and TokenAuth.</summary>
<br/>

| Features | SessionAuth|  TokenAuth |
| -------- | ------------ | ---------|
| Storage | Session ID in server-side storage & cookie| Token stored on client-side (e.g., local storage)
| State | Stateful | Can be stateless (JWTs) or stateful (opaque tokens)
| Client Type | Primarily for browsers | Primarily for non-browser clients (APIs, mobile)
| Transmission | Automatic via cookies | Explicitly included in Authorization header
| CSRF | Vulnerable if unprotected, DRF provides protection| Not inherently vulnerable to CSRF
| Scalability | Can be less scalable for distributed systems| Generally more scalable for distributed systems
</details>

<details>
<summary>7. How do you write custom permissions in DRF?</summary>
<br/>

```python
from rest_framework import permissions

class IsOwner(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.owner == request.user
```
</details>





 
# Middleware

<details>
<summary>1. What is middleware in Django?</summary>
<br/>

</details>

<details>
<summary>2. Explain the order of middleware execution.</summary>
<br/>

</details>

<details>
<summary>3. How do you write a custom middleware?</summary>
<br/>

</details>


<details>
<summary>4. Give an example where middleware is useful.</summary>
<br/>

</details>


# Authentication & Authorization

<details>
<summary>1. How does Django handle authentication?</summary>
<br/>

</details>


<details>
<summary>2. Difference between AbstractUser and AbstractBaseUser.</summary>
<br/>

</details>


<details>
<summary>3. How do you implement role-based access control in Django?</summary>
<br/>

</details>


<details>
<summary>4. Explain Django’s session framework?</summary>
<br/>

</details>


<details>
<summary>4. How do you implement social authentication (Google/Facebook login)?</summary>
<br/>

</details>




# Caching & Performance
<details>
<summary>1. What caching strategies does Django support?</summary>
<br/>

</details>

<details>
<summary>1. Difference between per-view, per-site, and low-level caching?</summary>
<br/>

</details>

<details>
<summary>1. How do you cache database queries?</summary>
<br/>

</details>


How to use Redis or Memcached with Django?
How do you handle N+1 query problems in Django ORM?

# Testing
How do you write unit tests in Django?
Difference between TestCase and SimpleTestCase.
How do you test APIs in Django?
What is pytest-django?
How do you use Django’s test client?

# Deployment & DevOps
How do you deploy a Django app in production?
Difference between DEBUG=True and DEBUG=False.
How do you serve static and media files in production?
Explain how you would scale a Django app (load balancers, caching, DB replication).
What’s the role of WSGI and ASGI in Django?
Difference between Gunicorn and uWSGI.
How do you use Docker with Django?
How do you set up CI/CD for a Django project?

# Security
How does Django prevent CSRF attacks?
What are some common security middlewares in Django?
How do you prevent SQL Injection in Django?
How do you secure Django cookies (HttpOnly, Secure, SameSite)?
How do you protect sensitive data (API keys, DB credentials) in Django projects?

# System Design with Django
How would you design a scalable e-commerce system in Django?
How do you design a multi-tenant architecture in Django?
How would you handle millions of requests per day in Django?
How do you integrate Django with microservices?
How would you design a real-time chat app with Django + WebSockets?
# Advanced Django Topics
Explain Django Channels and its use cases.
Difference between synchronous and asynchronous views in Django 3+.
How to integrate Celery with Django for background tasks?
How do you use signals vs. event-driven architecture in Django?
How do you handle GraphQL in Django (Graphene)?
How do you integrate Django with external APIs?

# Behavioral / Project-Based Questions
Walk me through your biggest Django project.
What challenges did you face in scaling your Django app?
Have you optimized a slow Django query? How?
How did you handle authentication & authorization in your last project?
How did you set up CI/CD or cloud deployment for your Django app?
Have you worked with async features in Django?




