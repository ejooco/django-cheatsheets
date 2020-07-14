# Django Cheat Sheet 7 - Login and Logout System
By ejooco

## Create default login/logout views

django_project/urls.py


```python
from django.contrib import admin
from django.contrib.auth import views as auth_views
from django.urls import path, include
from users import views as user_views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('register/', user_views.register, name='register'),
    path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout'),
    path('', include('blog.urls')),
]
```

Create users/templates/users/login.html


```python
{% extends "blog/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
        <div class="content-section">
            <form method="POST">
                {% csrf_token %}
                <fieldset class="form-group">
                    <legend class="border-bottom mb-4">Log In</legend>
                    {{ form|crispy }}
                </fieldset>
                <div class="form-group">
                    <button class="btn btn-outline-info" type="submit">Login</button>
                </div>
            </form>
            <div class="border-top pt-3">
                <small class="text-muted">
                    Need An Account? <a class="ml-2" href="{% url 'register' %}">Sign Up Now</a>
                </small>
            </div>
        </div>
{% endblock content %}
```

Create users/templates/users/logout.html


```python
{% extends "blog/base.html" %}
{% block content %}
<h2>You have been logged out</h2>
<div class="border-top pt-3">
    <small class="text-muted">
        <a href="{% url 'login' %}">Log In Again</a>
    </small>
</div>

{% endblock content %}
```

django_project/settings.py

Add the following line to the bottom


```python
LOGIN_REDIRECT_URL = 'blog-home'
```

users/views.py

update the redirect and message


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
            messages.success(request, f'Your account has been created. You can now login')
            return redirect('login')
    else:
        form = UserRegisterForm()
    return render(request, 'users/register.html', {'form': form})
```

## Change navbar based on whether or not user is logged in/out

blog/templates/blog/base.html

update navbar with following code


```python
<div class="navbar-nav">
    {% if user.is_authenticated %}
        <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
    {% else %}
        <a class="nav-item nav-link" href="{% url 'login' %}">Login</a>
        <a class="nav-item nav-link" href="{% url 'register' %}">Register</a>
    {% endif %}
</div>
```

## Setup routing so that users can only access certain pages after login

users/views.py | Underneath the register template


```python
def profile(request):
    return render(request, 'users/profile.html')
```

create users/templates/users/profile.html


```python
{% extends "blog/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
    <h1>{{ user.username }}</h1>
{% endblock content %}
```

Note above that the user does not need to be passed in as context. The user class is built in to Django and represents the current logged in user

### Create the route to the profile

django_project/urls.py


```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('register/', user_views.register, name='register'),
    path('profile/', user_views.profile, name='profile'),
    path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout'),
    path('', include('blog.urls')),
]
```

### Add profile link next to logout for logged in user

blog/templates/blog/base.html


```python
<div class="navbar-nav">
    {% if user.is_authenticated %}
        <a class="nav-item nav-link" href="{% url 'profile' %}">Profile</a>
        <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
    {% else %}
        <a class="nav-item nav-link" href="{% url 'login' %}">Login</a>
        <a class="nav-item nav-link" href="{% url 'register' %}">Register</a>
    {% endif %}
</div>
```

### Add login required decorator to prevent logged out users going back to the profile page without being logged in #cybersecurity

users/views.py


```python
from django.contrib.auth.decorators import login_required
```


```python
@login_required
def profile(request):
    return render(request, 'users/profile.html')
```

django_project/settings.py


```python
LOGIN_URL = "login"
```


```python

```


```python

```


```python

```
