# Django ORM

Consider the following example:
```
# models.py ✍🏻 One Author can have many Book 📕
class Author(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()

# Book have one author
class Book(models.Model):
    title = models.CharField(max_length=200)
    published_year = models.IntegerField()
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```

### 1. Basic Retrieval

**1.`all()`:** → Get all records
```
books = Book.objects.all()
for book in books:
    print(book.title)
```
***📌 SQL Equivalent:***
`SELECT * FROM book;`


**2.`get()`** → Fetch a single record (raises error if not found or multiple found)
```
author = Author.objects.get(id=1)
print(author.name)
```
***📌 SQL Equivalent:***
`SELECT * FROM author WHERE id = 1;`

**3. `filter()`** → Fetch multiple records matching condition

```
books = Book.objects.filter(published_year__gte=2020)
for book in books:
    print(book.title)
```
***📌 SQL Equivalent:***
`SELECT * FROM book WHERE published_year >= 2020;`

**4. `exclude()`** → Fetch records that do not match condition
```
old_books = Book.objects.exclude(published_year__gte=2020)
```
***📌 SQL Equivalent:***
`SELECT * FROM book WHERE published_year < 2020;`

**5.`order_by()`** → Sort records
```
books = Book.objects.all().order_by("-published_year")  # Descending
```
***📌 SQL Equivalent:***
`SELECT * FROM book ORDER BY published_year DESC;`

**6.`values()`** → Return dictionaries instead of objects
```
books = Book.objects.values("title", "published_year")
print(list(books)) # [{'title': 'Django Basics', 'published_year': 2022}, ...]
```

***📌 SQL Equivalent:***
`SELECT title, published_year FROM book;`

**7.`values_list()`** → Return tuples
```
titles = Book.objects.values_list("title", flat=True)
print(list(titles))
```
***📌 SQL Equivalent:***
`SELECT title FROM book;`

**8.`distinct()`** → Remove duplicates
```
years = Book.objects.values_list("published_year", flat=True).distinct()
print(list(years))
```
***📌 SQL Equivalent:***
`SELECT DISTINCT published_year FROM book;`

### 2. Creation, updation & delete


**1.`create()`** → Create and save in one step
```
author = Author.objects.create(name="Alice", age=35)
print(author.id)  # Auto-generated primary key
```
***📌 SQL Equivalent:***
`INSERT INTO author (name, age) VALUES ('Alice', 35);`

**2.`get_or_create()`** → Fetch if exists, else create
```
author, created = Author.objects.get_or_create(
    name="Bob",
    defaults={"age": 40}
)
print(created)  # True if a new record was created
```

**3.`update_or_create()`** → Update if exists, else create
```
author, created = Author.objects.update_or_create(
    name="Charlie",
    defaults={"age": 45}
)
```

**4.`update()`** → Bulk update on queryset
```
Author.objects.filter(name="Alice").update(age=36)
```
***📌 SQL Equivalent:***
`UPDATE author SET age = 36 WHERE name = 'Alice';`

**5.`bulk_create()`** → Insert multiple rows efficiently
```
Book.objects.bulk_create([
    Book(title="Django 101", published_year=2022, author=author),
    Book(title="Advanced Django", published_year=2023, author=author),
])
```
***📌 SQL Equivalent:***
`INSERT INTO book (title, published_year, author_id) VALUES (...), (...);`

**6.`save()`** → Create or update instance
```
author = Author(name="David", age=28)
author.save()  # INSERT

author.age = 29
author.save()  # UPDATE
```

**7.`delete()`** → Delete a single object
```
author = Author.objects.get(name="Alice")
author.delete()
```
***📌 SQL Equivalent:***
`DELETE FROM author WHERE name = 'Alice';`

**8.`delete()`** → Delete multiple objects (bulk delete)
```
Author.objects.filter(age__lt=30).delete()
```
***📌 SQL Equivalent:***
`DELETE FROM author WHERE age < 30;`


**9.`delete()`** → Delete all objects
```
Book.objects.all().delete()
```
***📌 SQL Equivalent:***
`DELETE FROM book;`

### 3. Lookup & Filtering
**1. Exact match**
```
Author.objects.filter(name__exact="Alice")
```
***📌 SQL*** → `SELECT * FROM Author WHERE name = 'Alice'`

**2. Case-insensitive match**
```
Author.objects.filter(name__iexact="alice")
```
***📌 SQL*** → `SELECT * FROM Author WHERE LOWER(name) = 'alice'`

**3. Contains / icontains (substring search)**
```
Book.objects.filter(title__icontains="django")
```
***📌 SQL*** → SELECT * FROM Book WHERE LOWER(title) LIKE '%django%'

**4. IN lookup**
```
Author.objects.filter(age__in=[25, 30, 35])
```
**📌 SQL** → `SELECT * FROM Author WHERE age IN (25, 30, 35)`

**5. Greater than / Less than**
```
Book.objects.filter(published_year__gte=2020)
```
***📌 SQL*** → `SELECT * FROM Book WHERE published_year >= 2020`

**6. Startswith / Endswith**
```
Author.objects.filter(name__startswith="A")
```
***📌 SQL*** → `SELECT * FROM Author WHERE name LIKE 'A%'`

**7. IsNull lookup**
```
Book.objects.filter(author__isnull=True)
```
***📌 SQL*** → `SELECT * FROM Book WHERE author_id IS NULL`

**8. Range lookup**
```
Book.objects.filter(published_year__range=(2019, 2022))
```
***📌 SQL*** → `SELECT * FROM Book WHERE published_year BETWEEN 2019 AND 2022`

### 4. Aggregation
aggregate() works on the entire queryset and returns a single value (dictionary).
**from django.db.models import Avg, Max, Min, Sum, Count**

**1. Average author age**
```
Author.objects.aggregate(avg_age=Avg("age"))
# {'avg_age': 32.5}
```
***📌 SQL*** → `SELECT AVG(age) FROM author;`

**2. Total number of books**
```
Book.objects.aggregate(total=Count("id"))
# {'total': 20}
```
***📌 SQL*** → `SELECT COUNT(id) FROM book;`

**3. Latest published year**
```
Book.objects.aggregate(latest=Max("published_year"))
# {'latest': 2023}
```
***📌 SQL*** → `SELECT MAX(published_year) FROM book;`


### 5. Annotation
annotate() adds an extra computed field per object in a queryset.
**from django.db.models import Count, Avg,**
**from django.db.models.functions import Length**

**1. Count related objects**
```
authors = Author.objects.annotate(num_books=Count("book"))
for a in authors:
    print(a.name, a.num_books)
```
***📌 SQL*** →
`SELECT author.*, COUNT(book.id) AS num_books
FROM author
LEFT JOIN book ON book.author_id = author.id
GROUP BY author.id;`

**2. Average of related objects**
```
authors = Author.objects.annotate(avg_pub_year=Avg("book__published_year"))
for a in authors:
    print(a.name, a.avg_pub_year)
```
**3. Complex annotation (length of name)**
```
authors = Author.objects.annotate(name_len=Length("name"))
```

### 6. Query Optimization
**1. `select_related`:**
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

**2. `prefetech_related`:**
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

### Transaction Control

Django wraps each HTTP request in a transaction by default when `ATOMIC_REQUESTS=True` (per DB).

But you can also manually control transactions using `transaction.atomic()`.

**1. Using `atomic()` (all-or-nothing block)**
If an error occurs inside the block, all queries are rolled back.

```
from django.db import transaction
from myapp.models import Author, Book

def create_author_with_books():
    try:
        with transaction.atomic():
            # Create author
            author = Author.objects.create(name="Alice", age=35)

            # Create first book
            Book.objects.create(title="Django Basics", published_year=2022, author=author)

            # Simulate an error (ZeroDivisionError)
            1 / 0  

            # This book will never be saved (rollback happens)
            Book.objects.create(title="Django Advanced", published_year=2023, author=author)

    except Exception as e:
        print("Transaction rolled back:", e)
```
📌 Effect: Both the author and first book are **not saved**, because of rollback.

**2. Savepoint Example**
You can use savepoints inside a transaction to rollback partially.
```
from django.db import transaction

def create_partial():
    with transaction.atomic():
        author = Author.objects.create(name="Bob", age=40)

        # Create savepoint
        sid = transaction.savepoint()

        try:
            Book.objects.create(title="Good Book", published_year=2020, author=author)
            1 / 0  # Error
            Book.objects.create(title="Another Book", published_year=2021, author=author)
        except:
            # Rollback only to savepoint (not full transaction)
            transaction.savepoint_rollback(sid)

        # This still commits (author + transaction before savepoint)
        Book.objects.create(title="Final Book", published_year=2022, author=author)
```

📌 Effect:
- `author` and `"Final Book"` are saved.
- `"Good Book"` is rolled back because of error inside savepoint.

**3. Enforcing Atomicity in Views**
You can also decorate a Django view:

```
from django.db import transaction
from django.http import JsonResponse

@transaction.atomic
def create_author(request):
    Author.objects.create(name="Charlie", age=30)
    Book.objects.create(title="New Book", published_year=2023, author_id=99999)  # Fails
    return JsonResponse({"msg": "done"})
```
📌 Effect: If second query fails (invalid author_id), **no changes are saved**
