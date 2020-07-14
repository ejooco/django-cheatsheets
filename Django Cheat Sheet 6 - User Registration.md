# Django Cheat Sheet 6

By ejooco

## User Registration

This is so that users can register to use the site and create material. This is different to the users within the admin page.

Start by creating a 'users' app


```python
python manage.py startapp users
```

Add the app to the root settings

django_project/settings.py


```python
INSTALLED_APPS = [
    'users.apps.UsersConfig',
    'blog.apps.BlogConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Create a register view

users/views.py


```python
from django.shortcuts import render
from django.contrib.auth.forms import UserCreationForm

def register(request):
    form = UserCreationForm()
    return render(request, 'users/register.html', {'form': form})
```

Create users/templates/users/register.html


```python
{% extends "blog/base.html" %}
{% block content %}
        <div class="content-section">
            <form method="POST">
                {% csrf_token %}
                <fieldset class="form-group">
                    <legend class="border-bottom mb-4">Join Today</legend>
                    {{ form.as_p }}
                </fieldset>
                <div class="form-group">
                    <button class="btn btn-outline-info" type="submit">Sign Up</button>
                </div>
            </form>
            <div class="border-top pt-3">
                <small class="text-muted">
                    Already Have An Account? <a class="ml-2" href="#">Sign In</a>
                </small>
            </div>
        </div>
{% endblock content %}
```

## Create url pattern to navigate to this page

We don't have to create a users/urls.py. Instead we can just import the users/views.py to django_project/urls.py

django_project/urls.py


```python
from django.contrib import admin
from django.urls import path, include
from users import views as user_views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('register/', user_views.register, name='register'),
    path('', include('blog.urls')),
]
```

## Send the form data somewhere

users/views.py


```python
from django.shortcuts import render, redirect
from django.contrib.auth.forms import UserCreationForm
from django.contrib import messages

def register(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            form.save()
            username = form.cleaned_data.get('username')
            messages.success(request, f'Account created for {username}')
            return redirect('blog-home')
    else:
        form = UserCreationForm()
    return render(request, 'users/register.html', {'form': form})
```

## Add flashed messages to blog/templates/blog/base.html
### to make sure all messages work on all pages

blog/templates/blog/base.html

Add the following above the {% block content %}


```python
    <div class="col-md-8">
        {% if messages %}
            {% for message in messages %}
                <div class="alert alert-{{ message.tags }}">
                    {{ message }}
                </div>
            {% endfor %}
        {% endif %}
      {% block content %}{% endblock %}
    </div>
```

## Personalize the registration form

create users/forms.py


```python
from django import forms
from django.contrib.auth.models import User
from django.contrib.auth.forms import UserCreationForm

class UserRegisterForm(UserCreationForm):
    email = forms.EmailField()

    class Meta:
        model = User
        fields = ['username', 'email', 'password1', 'password2']
```

### Add new form to views.py

Replace instances of UserCreationForm with our new UserRegisterForm

users/views.py


```python
from django.shortcuts import render, redirect
from django.contrib import messages
from .forms import UserRegisterForm

def register(request):
    if request.method == 'POST':
        form = UserRegisterForm(request.POST)
        if form.is_valid():
            form.save()
            username = form.cleaned_data.get('username')
            messages.success(request, f'Account created for {username}')
            return redirect('blog-home')
    else:
        form = UserRegisterForm()
    return render(request, 'users/register.html', {'form': form})
```

## Adjust the styling

Install crispy forms


```python
pip install django-crispy-forms
```

django_project/settings.py


```python
INSTALLED_APPS = [
    'users.apps.UsersConfig',
    'crispy_forms',
    'blog.apps.BlogConfig',
```

Add the CRISPY_TEMPLATE_PACK line after STATIC_URL


```python
STATIC_URL = '/static/'

CRISPY_TEMPLATE_PACK = 'bootstrap4'
```

users/templates/users/register.html

Load the crispy forms under the extends tag at the top and add the crispy tag after the form codeblock


```python
{% extends "blog/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
        <div class="content-section">
            <form method="POST">
                {% csrf_token %}
                <fieldset class="form-group">
                    <legend class="border-bottom mb-4">Join Today</legend>
                    {{ form|crispy }}
                </fieldset>
                <div class="form-group">
                    <button class="btn btn-outline-info" type="submit">Sign Up</button>
                </div>
            </form>
            <div class="border-top pt-3">
                <small class="text-muted">
                    Already Have An Account? <a class="ml-2" href="#">Sign In</a>
                </small>
            </div>
        </div>
{% endblock content %}
```


```python

```


```python

```


```python

```


```python

```


```python

```
