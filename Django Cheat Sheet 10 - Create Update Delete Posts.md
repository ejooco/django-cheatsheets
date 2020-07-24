# Django Cheat Sheet 10 - Create Update Delete Posts
### by ejooco


Up to this point, all of our views have been function based.
We will now create some class based views.
https://docs.djangoproject.com/en/3.0/topics/class-based-views/
**blog/views.py**
```python
from django.shortcuts import render
from django.contrib.auth.mixins import LoginRequiredMixin, UserPassesTestMixin
#used for login req for class based views and stopping anyone updating someone else's post
from django.views.generic import (
    ListView,
    DetailView,
    CreateView,
    UpdateView,
    DeleteView
)
from .models import Post

def home(request):
    context = {
        'posts': Post.objects.all()
    }
    return render(request, 'blog/home.html', context)

class PostListView(ListView):
    model = Post
    template_name = 'blog/home.html' # chaning the default template from the class based view. default is <app>/<model>_<viewtype>.html
    context_object_name = 'posts' #pass the context into the class based view
    ordering = ['-date_posted'] #display posts newest to oldest

class PostDetailView(DetailView):
    model = Post

class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    fields = ['title', 'content']

    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)

class PostUpdateView(LoginRequiredMixin, UserPassesTestMixin, UpdateView):
    model = Post
    fields = ['title', 'content']

    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)

    def test_func(self):
        post = self.get_object()
        if self.request.user == post.author:
            return True
        return False

class PostDeleteView(LoginRequiredMixin, UserPassesTestMixin, DeleteView):
    model = Post
    success_url = '/'

    def test_func(self):
        post = self.get_object()
        if self.request.user == post.author:
            return True
        return False

def about(request):
    return render(request, 'blog/about.html', {'title': 'About'})
```

**blog/urls.py**
```python
from django.urls import path
from .views import (
    PostListView,
    PostDetailView,
    PostCreateView,
    PostUpdateView,
    PostDeleteView
)
from . import views

urlpatterns = [
    path('', PostListView.as_view(), name='blog-home'),
    #<int:pk> tells django that we want to put in the primary key and that it should be an integer
    path('post/<int:pk>/', PostDetailView.as_view(), name='post-detail'),
    path('post/new/', PostCreateView.as_view(), name='post-create'),
    path('post/<int:pk>/update/', PostUpdateView.as_view(), name='post-update'),
    path('post/<int:pk>/delete/', PostDeleteView.as_view(), name='post-delete'),
    path('about/', views.about, name='blog-about'),
]
```
blog/templates/blog/post_detail.html
```
{% extends "blog/base.html" %}
{% block content %}
    <article class="media content-section">
        <img class="rounded-circle article-img" src="{{ object.author.profile.image.url }}">
        <div class="media-body">
            <div class="article-metadata">
                <a class="mr-2" href="#">{{ object.author }}</a>
                <small class="text-muted">{{ object.date_posted }}</small>
            </div>
            <h2 class="article-title" >{{ object.title }}</h2>
            <p class="article-content">{{ object.content }}</p>
        </div>
    </article>
{% endblock content %}
```

Create post_form.html (This is the name given to create and update which share a template)
blog/templates/blog/post_form.html
```
{% extends "blog/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
        <div class="content-section">
            <form method="POST">
                {% csrf_token %}
                <fieldset class="form-group">
                    <legend class="border-bottom mb-4">Blog Post</legend>
                    {{ form|crispy }}
                </fieldset>
                <div class="form-group">
                    <button class="btn btn-outline-info" type="submit">Post</button>
                </div>
            </form>
        </div>
{% endblock content %}
```

blog/templates/blog/post_confirm_delete.html
```
{% extends "blog/base.html" %}
{% block content %}
        <div class="content-section">
            <form method="POST">
                {% csrf_token %}
                <fieldset class="form-group">
                    <legend class="border-bottom mb-4">Delete Post</legend>
                    <h2>Are you sure you want to delete the post {{ object.title }}</h2>
                </fieldset>
                <div class="form-group">
                    <button class="btn btn-outline-danger" type="submit">Yes, Delete</button>
                    <a class="btn btn-outline-secondary" href="{% url 'post-detail' object.id %}">Cancel</a>
                </div>
            </form>
        </div>
{% endblock content %}
```

blog/models.py
```python
from django.db import models
from django.utils import timezone
from django.contrib.auth.models import User
from django.urls import reverse

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    date_posted = models.DateTimeField(default=timezone.now)
    author = models.ForeignKey(User, on_delete=models.CASCADE)

    def __str__(self):
        return self.title

    def get_absolute_url(self):
        return reverse('post-detail', kwargs={'pk': self.pk})
```

blog/templates/blog/home.html
```
{% extends "blog/base.html" %}
{% block content %}
        {% for post in posts %}
            <article class="media content-section">
                <img class="rounded-circle article-img" src="{{ post.author.profile.image.url }}">
                <div class="media-body">
                    <div class="article-metadata">
                        <a class="mr-2" href="#">{{ post.author }}</a>
                        <small class="text-muted">{{ post.date_posted }}</small>
                    </div>
                    <h2><a class="article-title" href="{% url 'post-detail' post.id %}">{{ post.title }}</a></h2>
                    <p class="article-content">{{ post.content }}</p>
                </div>
            </article>
        {% endfor %}
{% endblock content %}
```

Add routing to the navbar
blog/templates/blog/post_detail.html
```
{% extends "blog/base.html" %}
{% block content %}
    <article class="media content-section">
        <img class="rounded-circle article-img" src="{{ object.author.profile.image.url }}">
        <div class="media-body">
            <div class="article-metadata">
                <a class="mr-2" href="#">{{ object.author }}</a>
                <small class="text-muted">{{ object.date_posted }}</small>
                {% if object.author == user %}
                <div>
                <a class="btn btn-secondary btn-sm mt-1 mb-1" href="{% url 'post-update' object.id %}">Update</a>
                <a class="btn btn-danger btn-sm mt-1 mb-1" href="{% url 'post-delete' object.id %}">Delete</a>
                </div>
                {% endif %}
            </div>
            <h2 class="article-title" >{{ object.title }}</h2>
            <p class="article-content">{{ object.content }}</p>
        </div>
    </article>
{% endblock content %}
```


Within,
blog/templates/blog/base.html
```
                {% if user.is_authenticated %}
                    <a class="nav-item nav-link" href="{% url 'post-create' %}">New Post</a>
                    <a class="nav-item nav-link" href="{% url 'profile' %}">Profile</a>
                    <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
                {% else %}
                    <a class="nav-item nav-link" href="{% url 'login' %}">Login</a>
                    <a class="nav-item nav-link" href="{% url 'register' %}">Register</a>
                {% endif %}
```