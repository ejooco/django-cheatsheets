# Django Cheat Sheet 1
by ejooco
## Setting up a project and basic routing

This is the first of the Django Cheat Sheet series. This cheat sheet explains how to set up a Django project, create an app, and perform basic routing to the app.

### Create a virtual environment


```python
virtualenv venv
```

### Activate the virtual environment

Linux


```python
source venv/bin/activate
```

Windows


```python
.\venv\Scripts\activate.bat
```

### Install Django


```python
pip install django
```

### Start the Django project 'django_project'


```python
django-admin startproject django_project
```


```python
cd django_project
```

### Run the server to view the Django splash page


```python
python manage.py runserver
```

Navigate to http://127.0.0.1:8000 to view splash page

### Create app 'blog' within the project


```python
python manage.py startapp blog
```

## Creating views and setting up routing

### Create a view within the blog app

blog/views.py


```python
from django.shortcuts import render
from django.http import HttpResponse

def home(request):
    return HttpResponse('<h1>Blog Home</h1>')
```

### Create urls.py file within blog directory

blog/urls.py


```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='blog-home')
]
```

### Route to the blog

Add the include() function to the imports in the django_project/urls.py file so that the page can route to the new blogs web app

django_project/urls.py


```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')),
]
```

### Navigate to blog

navigate to http://127.0.0.1/blog

Note that navigating to http://127.0.0.1/ at this stage will return a 404 error

### Fix the 404

To fix the 404 error, within django_project/urls.py, change the above code so that 'blog/' is replaced with ''


```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('blog.urls')),
]
```

Now navigating to http://127.0.0.1 should route to the blog app
