# Django Cheat Sheet 8
### by ejooco

## User Profile and Picture

## Create the models

users/models.py

```python
from django.db import models
from django.contrib.auth.models import User

class Profile(models.Model):
    # One user per profile and vice versa
    # CASCADE if a user is deleted, so is the profile
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    image = models.ImageField(default='default.jpg', upload_to='profile_pics')

    def __str__(self):
        return f'{self.user.username} Profile'
```

You may need to pip install Pillow
```
pip install Pillow
```

Make the migrations
```
python manage.py makemigrations
```
Apply the changes to the database
```
python manage.py migrate
```

users/admin.py
```python
from django.contrib import admin
from .models import Profile

admin.site.register(Profile)
```

Add MEDIA_ROOT and MEDIA_URL to django_project/settings.py
```python
STATIC_URL = '/static/'

# where django stores uploaded files.
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URL = '/media/'
```

Run the server
```
python manage.py runserver
```
Go to admin page at http://127.0.0.1:8000/admin and login

Click Profile and Add Profile 
Add images to any desired profiles

users/templates/users/profile.html
```
{% extends "blog/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
    <div class="content-section">
      <div class="media">
        <img class="rounded-circle account-img" src="{{ user.profile.image.url }}">
        <div class="media-body">
          <h2 class="account-heading">{{ user.username }}</h2>
          <p class="text-secondary">{{ user.email }}</p>
        </div>
      </div>
      <!-- FORM HERE -->
    </div>
{% endblock content %}
```
django_project/urls.py

ABOVE urlpatterns
```python
from django.conf import settings
from django.conf.urls.static import static
```
BELOW urlpatterns
```python
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

add default.jpg to media/

### Create profile every time a user is created
#### Django Signals

Create users/signals.py
```python
from django.db.models.signals import post_save
from django.contrib.auth.models import User
from django.dispatch import receiver
from .models import Profile


@receiver(post_save, sender=User)
def create_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)


@receiver(post_save, sender=User)
def save_profile(sender, instance, **kwargs):
    instance.profile.save()
```
users/apps.py
```python
from django.apps import AppConfig


class UsersConfig(AppConfig):
    name = 'users'

    def ready(self):
        import users.signals
```









