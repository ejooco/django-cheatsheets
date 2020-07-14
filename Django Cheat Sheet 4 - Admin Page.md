# Django Cheat Sheet 4
By ejooco
## Admin Page

Navigate to http://127.0.0.1/admin/

You will not be able to log in as there are no users. First we need to create the auth_table within the database


```python
python manage.py makemigrations
```


```python
python manage.py migrate
```


```python
python manage.py createsuperuser
```

Create name and password


```python
python manage.py runserver
```

Navigate to http://127.0.0.1/admin/

Login with new login details

## Register DB models to the admin page

Assuming you have a DB model/class within blog/models.py, register them within blog/admin.py to make them accessible from the admin page

blog/admin.py


```python
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```
