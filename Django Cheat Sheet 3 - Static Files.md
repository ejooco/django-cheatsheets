# Django Cheat Sheet 3
By ejooco
## Static Files

Create static folder within the main directory of the blog app. Create another folder within that called blog

We are creating blog/static/blog/


```python
!tree
```

    [01;34m.[00m
    ├── [01;34mblog[00m
    │   ├── admin.py
    │   ├── apps.py
    │   ├── __init__.py
    │   ├── [01;34mmigrations[00m
    │   │   ├── __init__.py
    │   │   └── [01;34m__pycache__[00m
    │   │       └── __init__.cpython-37.pyc
    │   ├── models.py
    │   ├── [01;34m__pycache__[00m
    │   │   ├── admin.cpython-37.pyc
    │   │   ├── apps.cpython-37.pyc
    │   │   ├── __init__.cpython-37.pyc
    │   │   ├── models.cpython-37.pyc
    │   │   ├── urls.cpython-37.pyc
    │   │   └── views.cpython-37.pyc
    │   ├── [01;34mstatic[00m
    │   │   └── [01;34mblog[00m
    │   ├── [01;34mtemplates[00m
    │   │   └── [01;34mblog[00m
    │   │       ├── about.html
    │   │       ├── base.html
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
    
    10 directories, 29 files


## CSS

Create blog/static/blog/main.css

Add snippet to main.css

https://github.com/CoreyMSchafer/code_snippets/blob/master/Django_Blog/snippets/main.css

Link blog/static/main.css to blog/templates/blog/base.html

blog/templates/blog/base.html


```python
# Add the load static line to the top of the file
{% load static %}
<!DOCTYPE html>
```


```python
 <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
    <link rel="stylesheet" type="text/css" href="{% static 'blog/main.css' %}">
    #Add the bottom link line to link the stylesheet
```
