# Django Cheat Sheet 9 - Update User Profile
### By ejooco

## Allowing users to update their profile on the front-end

### Create some more forms
Creating a model form that will work with a specific database model


users/forms.py

add to imports
```python
from .models import Profile
```

add form
```python

class UserUpdateForm(forms.ModelForm):
    email = forms.EmailField()

    class Meta:
        model = User
        fields = ['username', 'email']


class ProfileUpdateForm(forms.ModelForm):
    class Meta:
        model = Profile
        field = ['image']
```

open **users/views.py** to add forms to views

add to imports
```python
from .forms import UserRegisterForm, UserUpdateForm, ProfileUpdateForm
```

add to profile function
```python
@login_required
def profile(request):
    if request.method == 'POST':
        u_form = UserUpdateForm(request.POST, instance=request.user)
        p_form = ProfileUpdateForm(request.POST,
                                   request.FILES,
                                   instance=request.user.profile)
        if u_form.is_valid() and p_form.is_valid():
            u_form.save()
            p_form.save()
            messages.success(request, f'Your account has been updated')
            return redirect('profile')
    else:
        u_form = UserUpdateForm(instance=request.user)
        p_form = ProfileUpdateForm(instance=request.user.profile)

    context = {
        'u_form': u_form,
        'p_form': p_form
    }

    return render(request, 'users/profile.html', context)
```

add form to **users/templates/users/profile.html
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
      <form method="POST" enctype="multipart/form-data">
          {% csrf_token %}
          <fieldset class="form-group">
              <legend class="border-bottom mb-4">Profile Info</legend>
              {{ u_form|crispy }}
              {{ p_form|crispy }}
          </fieldset>
          <div class="form-group">
              <button class="btn btn-outline-info" type="submit">Update</button>
          </div>
      </form>
    </div>
{% endblock content %}
```

Resize images using Pillow
users/models.py

import PIL and update profile class
```python
from django.db import models
from django.contrib.auth.models import User
from PIL import Image

class Profile(models.Model):
    # One user per profile
    # CASCADE if a user is deleted, so is the profile
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    image = models.ImageField(default='default.jpg', upload_to='profile_pics')

    def __str__(self):
        return f'{self.user.username} Profile'

    def save(self):
        super().save()

        img = Image.open(self.image.path)

        if img.height > 300 or img.width > 300:
            output_size = (300, 300)
            img.thumbnail(output_size)
            img.save(self.image.path)
```

Add image to blog posts in blog/templates/blog/homt.html
```
{% extends "blog/base.html" %}
{% block content %}
        {% for post in posts %}
            <article class="media content-section">
                <img class="rounded-circle article-img" src="{{ post.author.profile.image.url }}"
                <div class="media-body">
                    <div class="article-metadata">
                        <a class="mr-2" href="#">{{ post.author }}</a>
                        <small class="text-muted">{{ post.date_posted }}</small>
                    </div>
                    <h2><a class="article-title" href="#">{{ post.title }}</a></h2>
                    <p class="article-content">{{ post.content }}</p>
                </div>
            </article>
        {% endfor %}
{% endblock content %}
```


