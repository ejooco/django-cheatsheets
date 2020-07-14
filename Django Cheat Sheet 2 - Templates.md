# Django Cheat Sheet 2
by ejooco
https://www.youtube.com/watch?v=qDwdMDQ8oX4&list=PL-osiE80TeTtoQCKZ03TU5fNfx2UY6U4p&index=3
Explanations and Credit go to Corey Schafer's youtube page

## Templates

### Create Templates directory within the app

Create a templates directory within the blog application

blog/templates/

Now create another directory within the templates directory called 'blog'. This seems a bit silly but it's how Django works

blog/templates/blog/

Create home.html and about.html within the blog/templates/blog/ folder


```python
!tree
```

    [01;34m.[00m
    ├── [01;34mblog[00m
    │   ├── admin.py
    │   ├── apps.py
    │   ├── __init__.py
    │   ├── [01;34mmigrations[00m
    │   │   └── __init__.py
    │   ├── models.py
    │   ├── [01;34m__pycache__[00m
    │   │   ├── __init__.cpython-37.pyc
    │   │   ├── urls.cpython-37.pyc
    │   │   └── views.cpython-37.pyc
    │   ├── [01;34mtemplates[00m
    │   │   └── [01;34mblog[00m
    │   │       ├── about.html
    │   │       └── home.html
    │   ├── tests.py
    │   ├── urls.py
    │   └── views.py
    ├── db.sqlite3
    ├── [01;34mdjango_project[00m
    │   ├── asgi.py
    │   ├── __init__.py
    │   ├── [01;34m__pycache__[00m
    │   │   ├── __init__.cpython-37.pyc
    │   │   ├── settings.cpython-37.pyc
    │   │   ├── urls.cpython-37.pyc
    │   │   └── wsgi.cpython-37.pyc
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    └── [01;32mmanage.py[00m
    
    7 directories, 24 files


### Add blog application to list of installed apps

This is so that Django knows to look here for the templates directory

Add app configuration to projects settings.py module

blog/apps.py


```python
from django.apps import AppConfig


class BlogConfig(AppConfig):
    name = 'blog'
```

copy BlogConfig name from apps.py file (shown above) and add it to settings.py INSTALLED_APPS

django_project/settings.py


```python
INSTALLED_APPS = [
    'blog.apps.BlogConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

### Adjust functions within blog/views.py to render templates instead of HttpResponse functions

blog/views.py


```python
from django.shortcuts import render

def home(request):
    return render(request, 'blog/home.html')
```

blog/templates/blog/home.html


```python
<!DOCTYPE html>
<html>
    <head>
        <title></title>
    </head>
    <body>
        <h1>Blog home!</h1>
    </body>
</html>
```


```python

```


```python

```

## Create data for the templates to process. Move away from static pages

blog/views.py

Add some fake data


```python
posts = [
    {
        'author': 'ejooco',
        'title': 'Blog Post 1',
        'content': 'First Post content',
        'date_posted': ' 09 July 2020'
    },
    {
        'author': 'bear the dog',
        'title': 'favourite kibble',
        'content': 'dogs rule',
        'date_posted': ' 09 July 2020'
    }
]
```

Pass the data into the template by adjusting the home() function


```python
def home(request):
    context = {
        'posts': posts
    }
    return render(request, 'blog/home.html', context)
```

blog/templates/blog/home.html


```python
<!DOCTYPE html>
<html>
    <head>
        <title></title>
    </head>
    <body>
        {% for post in posts %}
            <h1>{{ post.title }}</h1>
            <p>By {{ post.author }}, {{ post.date_posted }}</p>
            <p>{{ post.content }}</p>
        {% endfor %}
    </body>
</html>
```

## Template Inheritance

Create base.html inside of blog/templates/blog/

/blog/templates/blog/base.html


```python
<!DOCTYPE html>
<html>
<head>
    {% if title %}
        <title>Django Blog - {{ title }}</title>
    {% else %}
        <title>Django Blog</title>
    {% endif %}
</head>
<body>
    {% block content %}{% endblock %}
</body>
</html>
```

/blog/templates/blog/home.html


```python
{% extends "blog/base.html" %}
{% block content %}
        {% for post in posts %}
            <h1>{{ post.title }}</h1>
            <p>By {{ post.author }}, {{ post.date_posted }}</p>
            <p>{{ post.content }}</p>
        {% endfor %}
{% endblock content %}
```

### Self updating title

Make another html file withing blog/templates/blog/

/blog/templates/blog/about.html


```python
{% extends "blog/base.html" %}
{% block content %}
        <h1>About Page</h1>
{% endblock content %}
```

blog/views.py


```python
def about(request):
    return render(request, 'blog/about.html', {'title': 'About'})
```

Here we pass in a dictionary as the third parameter. This is used by the base.html code to decide what to render as the title.

As seen below

/blog/templates/blog/base.html


```python
<head>
    {% if title %}
        <title>Django Blog - {{ title }}</title>
    {% else %}
        <title>Django Blog</title>
    {% endif %}
</head>
```

# Add Bootstrap
https://getbootstrap.com/docs/4.5/getting-started/introduction/

Add bootsrap requirements to blog/templates/blog/base.html and wrap block content in a div with class container

blog/templates/blog/base.html


```python
<!DOCTYPE html>
<html>
<head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">

    {% if title %}
        <title>Django Blog - {{ title }}</title>
    {% else %}
        <title>Django Blog</title>
    {% endif %}
</head>
<body>
    <div class="container">
        {% block content %}{% endblock %}
    </div>

<!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/js/bootstrap.min.js" integrity="sha384-OgVRvuATP1z7JjHLkuOU7Xw704+h835Lr+6QL9UvYjZE3Ipu6Tp75j7Bh/kR0JKI" crossorigin="anonymous"></script>
</body>
</html>
```

## Navigation and Sidebar snippets

https://github.com/CoreyMSchafer/code_snippets/blob/master/Django_Blog/snippets/navigation.html

https://github.com/CoreyMSchafer/code_snippets/blob/master/Django_Blog/snippets/main.html

Add above code snippets to blog/templates/blog/base.html

blog/templates/blog/base.html

## Add links to base.html


```python
<a class="nav-item nav-link" href="{% url 'blog-home' %}">Home</a>
```

This will link the Home button in the navbar to:


```python
# blog/urls.py
urlpatterns = [
    path('', views.home, name='blog-home'),
    path('about/', views.about, name='blog-about'),
]
```


```python

```
