# Django Cheat Sheet 5
by ejooco
## Models and Databases

https://www.youtube.com/watch?v=aHC3uTkT9r8&list=PL-osiE80TeTtoQCKZ03TU5fNfx2UY6U4p&index=5

Video Tutorial by Corey Schafer from his Youtube page

Each class is it's own table in the database

Each attribute is it's own field in the database

blog/models.py


```python
from django.db import models
from django.utils import timezone
from django.contrib.auth.models import User

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    date_posted = models.DateTimeField(default=timezone.now)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    
    def __str__(self):
        return self.title
```

### Make Migrations


```python
python manage.py makemigrations
```


```python
python manage.py migrate
```

## Query DB using Django shell
This isn't required. Just shows how Django will process things


```python
python manage.py shell
```


```python
>>> from blog.models import Post
>>> from django.contrib.auth.models import User
>>> User.objects.all() #returns all users
>>> User.objects.first() #returns first user
>>> User.objects.last()
>>> User.objects.filter(username='testuser)
<QuerySet [<User: testuser>]>
>>> User.objects.filter(username='testuser').first()
<User: testuser>
```


```python
>>> user = User.objects.filter(username='testuser').first()
>>> user
<User: testuser>
>>> user.id
1
>>> user.pk
1
>>> user = User.objects.get(id=1)
>>> user
<User: testuser>
```

## Remove dummy data and pass in information from the db
Note that there is no data in there ey

Import the Post class from models.py

blog/views.py


```python
from django.shortcuts import render
from .models import Post

def home(request):
    context = {
        'posts': Post.objects.all()
    }
    return render(request, 'blog/home.html', context)

def about(request):
    return render(request, 'blog/about.html', {'title': 'About'})
```


```python

```


```python

```


```python

```
